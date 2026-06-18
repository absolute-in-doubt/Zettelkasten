
13-06-2026 20:28

Status:

Tags: [[Spring Security]] [[Spring]]

---
# Oauth2-authorization-server overview Spring Security

```xml
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-oauth2-authorization-server</artifactId>
</dependency>
```


**Для Spring Boot есть готовый стартер:**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-authorization-server</artifactId>
</dependency>
```

`spring-security-oauth2-authorization-server` — это реализация OAuth 2.1 / OpenID Connect Provider на базе Spring Security.

Он умеет:

- выдавать access token'ы;

- выдавать refresh token'ы;

- публиковать JWKS;

- выполнять OAuth2 Authorization Code Flow;

- выполнять Client Credentials Flow;

- работать как OIDC Provider (`/userinfo`, ID Token и т.д.);

- хранить зарегистрированных клиентов;

- хранить выданные токены и авторизации.

Основные бины (кратко):
```
RegisteredClient
OAuth2Authorization
OAuth2AuthorizationService
OAuth2TokenGenerator
JwtEncoder
JWKSource
AuthorizationServerSettings
```


---
# Основные Модели

#### [[RegisteredClient - Spring Security Oauth2 Authorization Server|RegisteredClient]]

#### [[Oauth2Authorization - Spring Security Oauth2 Authorization Server|Oauth2Authorization]]
#### [[Oauth2AuthorizationConsent - Spring Security Oauth2 Authorization Server|Oauth2AuthorizationConsent]]


---

# Основные абстракции


#### `OAuth2TokenGenerator`

Создает токены.

Главная точка расширения.

```java
OAuth2TokenGenerator<? extends OAuth2Token>
```

По умолчанию генерирует:

- access token;
- refresh token;
- id token.



Можно добавить свои claims:

```java
OAuth2TokenCustomizer<JwtEncodingContext>
```

Пример:

```java
@Bean
OAuth2TokenCustomizer<JwtEncodingContext> customizer() {
    return context -> {
        context.getClaims()
               .claim("userId", 123);
    };
}
```


---

# JwtEncoder

Подписывает JWT.

Использует `JWKSource<SecurityContext>`




---

# JWKSource

Источник ключей.

Используется для:

- подписи JWT;
- **~={red}публикации JWKS endpoint.=~**


Пример:

```java
@Bean
JWKSource<SecurityContext> jwkSource() {
    ...
}
```


---
### `AuthorizationServerSettings`

Настройки endpoint'ов сервера.

Пример:

```java
AuthorizationServerSettings
```

Позволяет изменить:

```text
/oauth2/token
/oauth2/jwks
/oauth2/revoke
/oauth2/introspect
```

Например:

```java
@Bean
AuthorizationServerSettings settings() {
    return AuthorizationServerSettings.builder()
        .issuer("https://auth.company.com")
        .build();
}
```



---

### `OAuth2AuthorizationServerConfigurer`

Главный конфигуратор SecurityFilterChain.

Пример:

```java
OAuth2AuthorizationServerConfigurer
```

Именно он регистрирует:

```text
/oauth2/token
/oauth2/authorize
/oauth2/jwks
/oauth2/introspect
/oauth2/revoke
```

и все нужные фильтры.

> [!note] 
> В `oauth2-resource-server` есть аналог:
> ```
> OAuth2ResourceServerConfigurer
> ```
> А именно:
> ```java
> http.oauth2ResourceServer(...)
> ```



----
#### [[Oauth2-authorization-server overview Spring Security - Flashcards|Link to flashcards]]



---
### References:

