
13-06-2026 20:46

Status:

Tags: [[Spring Security]] [[Spring]]

---
# RegisteredClient - Spring Security Oauth2 Authorization Server


### `RegisteredClient`

Аналог OAuth2-клиента ([[Clients - Keycloak]]).

Clients - приложения и сервисы, которые могут отправлять запрос к Authentication Service на аутентификацию пользователя или для получения access token.

Содержит:

- clientId
- clientSecret
- grant types
- redirect URIs
- scopes
- настройки токенов


Пример:

```java
RegisteredClient registeredClient =
    RegisteredClient.withId(UUID.randomUUID().toString())
        .clientId("web-client")
        .clientSecret("{noop}secret")
        .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
        .redirectUri("http://localhost:8080/login/oauth2/code/web-client")
        .scope("openid")
        .build();
```


---
### `RegisteredClientRepository`

Хранилище клиентов.

Аналогично [[UserDetailsService - Spring Security|UserDetailsService]],  но для OAuth-клиентов.

Встроенные реализации:

```java
InMemoryRegisteredClientRepository

JdbcRegisteredClientRepository
```



---
### `ClientSecretAuthenticationProvider`

AuthenticationProvider, который использует `RegisteredClientRepository` для получения данных о `RegisteredClient`.


---

### Пример настройки для авторизации Client


```java
@Bean
public RegisteredClientRepository registeredClientRepository() {

    RegisteredClient orderService =
            RegisteredClient.withId(UUID.randomUUID().toString())
                    .clientId("order-service")
                    .clientSecret("{noop}secret")
                    .authorizationGrantType(AuthorizationGrantType.CLIENT_CREDENTIALS)
                    .scope("orders.read")
                    .scope("orders.write")
                    .build();

    return new InMemoryRegisteredClientRepository(orderService);
}
```

Здесь мы говорим:

```
client_id     = order-service
client_secret = secret
grant_type    = client_credentials
```


Настраиваем Authorization Server:

```java
@Bean
public SecurityFilterChain authServerSecurityFilterChain(HttpSecurity http)
        throws Exception {

    OAuth2AuthorizationServerConfiguration.applyDefaultSecurity(http);

    return http.build();
}
```


Настраиваем ключ для подписи JWT:

```java
@Bean
public JWKSource<SecurityContext> jwkSource() {
	
    KeyPair keyPair = generateRsaKey();
	
    RSAPublicKey publicKey =
            (RSAPublicKey) keyPair.getPublic();
	
    RSAPrivateKey privateKey =
            (RSAPrivateKey) keyPair.getPrivate();
	
    RSAKey rsaKey = new RSAKey.Builder(publicKey)
            .privateKey(privateKey)
            .keyID(UUID.randomUUID().toString())
            .build();
	
    JWKSet jwkSet = new JWKSet(rsaKey);
	
    return (selector, context) ->
            selector.select(jwkSet);
}
```

Настраиваем issuer claim в JWT:

```java
@Bean
public AuthorizationServerSettings authorizationServerSettings() {
	
    return AuthorizationServerSettings.builder()
            .issuer("http://localhost:9000")
            .build();
}
```

## Как происходит аутентификация клиента внутри Spring

Для endpoint `/oauth2/token` автоматически подключается фильтр `OAuth2ClientAuthenticationFilter`

Он создает:

`OAuth2ClientAuthenticationToken` из заголовка получаемого запроса:
`Authorization: Basic base64(client_id:client_secret)`

или

```
client_id=...client_secret=...
```

затем вызывает [[AuthenticationManager - Spring Security|AuthenticationManager]]

↓

`ClientSecretAuthenticationProvider`

↓

`RegisteredClientRepository`

↓

проверка секрета.

----
#### [[RegisteredClient - Spring Security Oauth2 Authorization Service - Flashcards|Link to flashcards]]



---
### References:

- [[Oauth2-authorization-server overview - Spring Security]]