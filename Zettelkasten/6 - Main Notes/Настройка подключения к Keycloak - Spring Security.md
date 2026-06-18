
05-04-2026 08:23

Status:

Tags: [[Spring Security]] [[Keycloak]] [[Spring]]

---
# Настройка подключения к Keycloak - Spring Security

```yml
spring:  
  application:  
    name: eventapp  
  security:  
    oauth2:  
      client:  
        registration:  
          keycloak:  
            provider: keycloak  
            client-id: eventapp  
            client-secret: KOuDBZYZJpMqpIhJyZmFocrScEg4SBc6  
            client-name: eventapp  
            authorization-grant-type: authorization_code  
            scope: openid, profile, email  
        provider:  
          keycloak:  
            issuer-uri: http://localhost:9090/realms/proselyte  
            token-uri: http://localhost:9090/realms/proselyte/protocol/openid-connect/token  
		  
      resourceserver:  
        jwt:  
          issuer-uri: http://localhost:9090/realms/proselyte
```

Данные, передаваемые при [[Grant Types - Keycloak#Authorization Flow|запросе авторизации]]:

- `client-id` - id клиента
- `client-secret` - серкетный ключ для клиента. Получаем в keycloak админ панели.
- `client-name`
- `authorization-grant-type` - [[Grant Types - Keycloak|Grant Type]]
- `scope` - требуемые [[Client Scope - Keycloak|Client Scopes]], которые хотим получить в JWT


##### `resource_server`

`issuer-uri` - URI нашего Realm-а.



----


```java
@Bean  
public SecurityFilterChain filterChain(HttpSecurity http,  
		/*JwtVerificationFilter jwtVerificationFilter*/  
	   JwtAuthenticationConverter jwtAuthenticationConverter,  
	   BearerTokenResolver publicPathsBearerTokenResolver  
) throws Exception {  

	http  
			.sessionManagement(session -> session  
					.sessionCreationPolicy(SessionCreationPolicy.STATELESS) // отключает SessionManagementFilter  
			)  
//                .securityContext(Customizer.withDefaults())  
			.headers(Customizer.withDefaults())  
			.cors(Customizer.withDefaults())  
			.csrf(AbstractHttpConfigurer::disable)  
			.logout(AbstractHttpConfigurer::disable)  
			.requestCache(AbstractHttpConfigurer::disable)  
			.servletApi(Customizer.withDefaults())  
			.authorizeHttpRequests(authz -> authz  
					.requestMatchers(publicPaths.toArray(String[]::new)).permitAll()  
					.requestMatchers(privatePaths.toArray(String[]::new)).hasAnyAuthority("ROLE_enziberg.user", "ROLE_enziberg.subscribed.user", "ROLE_enziberg.admin")  
					.anyRequest().authenticated()  
			)  
			//.addFilterBefore(jwtVerificationFilter, AnonymousAuthenticationFilter.class)  
			.oauth2ResourceServer(oauth2 -> oauth2  
					.bearerTokenResolver(publicPathsBearerTokenResolver)  
					.jwt(jwt -> jwt.jwtAuthenticationConverter(new KeycloakJwtAuthenticationConverter()))  
			)  
			.anonymous(Customizer.withDefaults())  
			.exceptionHandling(Customizer.withDefaults());  
	return http.build();  
}  


@Bean  
public BearerTokenResolver publicPathsBearerTokenResolver() {  
	DefaultBearerTokenResolver delegate = new DefaultBearerTokenResolver();  
	AntPathMatcher matcher = new AntPathMatcher();  
	return request -> {  
		boolean isPublic = publicPaths.stream().anyMatch(p -> matcher.match(p, request.getServletPath()));  
		return isPublic ? null : delegate.resolve(request);  
	};  
}
```


---
### Как это работает? Что за фильтр создаётся?

`oauth2ResourceServer().jwt(...)` регистрирует `BearerTokenAuthenticationFilter` в filter chain. 
Это `OncePerRequestFilter`. 

Here's exactly what happens inside it:

```
BearerTokenAuthenticationFilter.doFilterInternal()
  │
  ├─ 1. bearerTokenResolver.resolve(request)   ← YOUR RESOLVER IS CALLED HERE
  │       └─ returns null for public paths → token = null
  │
  ├─ 2. if token == null → filterChain.doFilter() and RETURN
  │       └─ skips everything below, no auth attempt
  │
  ├─ 3. authenticationManager.authenticate(BearerTokenAuthenticationToken(token))
  │       └─ internally calls JwtDecoder.decode(token) → validates signature, expiry, etc.
  │
  ├─ 4. jwtAuthenticationConverter.convert(jwt)
  │       └─ your KeycloakJwtAuthenticationConverter extracts needed claims from the JWT, creates UsernameAndIdUserDetails and puts it into JwtAuthenticationToken
  │
  └─ 5. SecurityContextHolder.getContext().setAuthentication(jwtAuthenticationToken)
          └─ Authentication is now loaded into SecurityContext
```



В последствии, в контроллерах с помощью [[@AuthenticationPrincipal - Инъекция Authentication (UserDetails) в контроллере - Spring Security#Инжект текущего пользователя (@AuthenticationPrincipal)|@AuthenticationPrincipal]] инжектится именно `UsernameAndIdUserDetails`:

```java
@GetMapping("/payment/checkout-url")  
public ResponseEntity<String> getCheckoutSession(
	@AuthenticationPrincipal UsernameAndIdUserDetails userDetails
) throws JsonProcessingException {
	...
}
```

---
### Настройка извлечения ролей из JWT



```java
public class KeycloakJwtAuthenticationConverter implements Converter<Jwt, AbstractAuthenticationToken> {  
    @Override  
    public AbstractAuthenticationToken convert(Jwt jwt) {  
        List<String> roles = jwt.getClaimAsStringList("roles");  
        Collection<GrantedAuthority> authorities = roles == null ? List.of() :  
                roles.stream().map(SimpleGrantedAuthority::new).collect(Collectors.toList());  
  
        UsernameAndIdUserDetails userDetails = new UsernameAndIdUserDetails(jwt.getClaim("preferred_username"),jwt.getSubject()); 
  
        return new UsernamePasswordAuthenticationToken(userDetails, null, authorities);  
    }  
}
```



#### Настройка маппера

Нужно для того, чтобы resource roles маппились в верхнеуровневый claim `roles`.

![[Pasted image 20260405092951.png]]


### Так это выглядит в полчаемом JWT без маппинга

![[Pasted image 20260405093238.png]]


### А так - с маппингом

![[Pasted image 20260405093328.png]]

P.S.
	Старый claim `resource_access.eventapp.roles` тоже останется в JWT.


----
#### [[Настройка подключения к Keycloak - Spring Security - Flashcards|Link to flashcards]]



---
### References:

