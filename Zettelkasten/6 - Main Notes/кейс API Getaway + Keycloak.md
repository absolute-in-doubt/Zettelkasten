
25-04-2026 17:38

Status:

Tags: [[Keycloak]] 

---
# кейс API Getaway + Keycloak


1. **~={orange}The client (frontend/mobile app) authenticates with Keycloak directly and receives a JWT access token=~**


2. The client sends requests to your gateway with that token in the `Authorization: Bearer <token>` header


3. oauth2ResourceServer intercepts the request, extracts the token, and validates it — it needs to verify the token's signature against Keycloak's public key
	`+` для этого нужен специальный `JwtDecoder`, который будет декодировать JWT по нужному алгоритму:
```java
@Bean
public JwtDecoder jwtDecoder() {
    return NimbusJwtDecoder.withJwkSetUri("http://keycloak:8080/realms/<your-realm>/protocol/openid-connect/certs").build();
}
```


4. If valid, it converts the JWT claims into an `Authentication` object (via your `KeycloakJwtAuthenticationConverter`) and loads it into the `SecurityContext`


5. `authorizeHttpRequests` then checks whether the authenticated principal has the required authorities



#### `KeycloakJwtAuthenticationConverter`

```java
public class KeycloakJwtAuthenticationConverter implements Converter<Jwt, Collection<GrantedAuthority>> {  
    @Override  
    public Collection<GrantedAuthority> convert(Jwt jwt) {  
        List<String> roles = jwt.getClaimAsStringList("roles");  
        if(roles == null)  
            return List.of();  
  
        return roles.stream()  
                .map(SimpleGrantedAuthority::new)  
                .collect(Collectors.toList());  
    }  
}
```

---
### References:

