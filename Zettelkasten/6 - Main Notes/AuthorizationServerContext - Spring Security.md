
14-06-2026 10:27

Status:

Tags: [[Spring Security]] [[Spring]]

---
# AuthorizationServerContext - Spring Security


`AuthorizationServerContext` — это контекст текущего запроса к Spring Authorization Server.

По смыслу он очень похож на:

```java
SecurityContextHolder.getContext()
```

из обычного Spring Security, только хранит не информацию о пользователе, а информацию о самом Authorization Server. 
Для каждого запроса создаётся свой `AuthorizationServerContext`.


---
## Зачем нужен

Многие компоненты внутри Authorization Server должны знать:

- какой issuer использовать;
    
- какие endpoint'ы настроены;
    
- какие настройки сервера действуют для текущего запроса.

Передавать это через десяток параметров неудобно, поэтому появился `AuthorizationServerContext`.

---

## Что содержит

Интерфейс очень маленький:

```java
public interface AuthorizationServerContext {

    String getIssuer();

    AuthorizationServerSettings getAuthorizationServerSettings();
}
```

То есть фактически там хранится:

- issuer
- [[AuthorizationServerSettings - Spring Security|AuthorizationServerSettings]]


---
## Где хранится

Для каждого запроса создается:

```java
DefaultAuthorizationServerContext
```

и кладется в специальный holder:

```java
AuthorizationServerContextHolder
```

аналогично тому, как:

```java
SecurityContext
```

кладется в:

```java
SecurityContextHolder
```


---
### Аналогия

Обычный Spring Security:

```java
SecurityContextHolder
        ↓
SecurityContext
        ↓
Authentication
```

Authorization Server:

```java
AuthorizationServerContextHolder
        ↓
AuthorizationServerContext
        ↓
issuer + settings
```

---

## Как получить

Из любого кода:

```java
AuthorizationServerContext context =
        AuthorizationServerContextHolder.getContext();
```

Далее:

```java
String issuer = context.getIssuer();
```

или:

```java
AuthorizationServerSettings settings =
        context.getAuthorizationServerSettings();
```

---

## Где используется внутри Spring

Очень часто внутри генерации токенов.

Например, при создании JWT Spring автоматически берет:

```java
AuthorizationServerContextHolder
    .getContext()
    .getIssuer();
```

и записывает его в claim:

```json
{
  "iss": "https://auth.company.com"
}
```

---

Также используется при формировании OIDC Discovery документа:

```text
/.well-known/openid-configuration
```

где нужно вернуть:

```json
{
  "issuer": "...",
  "authorization_endpoint": "...",
  "token_endpoint": "...",
  ...
}
```

---

## Практический пример

Допустим, ты кастомизируешь JWT:

```java
@Bean
OAuth2TokenCustomizer<JwtEncodingContext> customizer() {
    return context -> {

        AuthorizationServerContext serverContext =
                AuthorizationServerContextHolder.getContext();

        context.getClaims().claim(
                "issuer_from_context",
                serverContext.getIssuer()
        );
    };
}
```

В токен попадет:

```json
{
  "issuer_from_context": "https://auth.company.com"
}
```

---

## Когда он нужен разработчику

В большинстве проектов — почти никогда.

Обычно ты работаешь с:

- `RegisteredClient`
    
- `OAuth2Authorization`
    
- `JwtEncodingContext`
    
- `Authentication`
    

и не трогаешь `AuthorizationServerContext`.

Он становится полезен, когда пишешь:

- собственные `AuthenticationProvider`;
    
- собственные `OAuth2TokenGenerator`;
    
- собственные endpoint'ы;
    
- сложную кастомизацию токенов.
    

Тогда через него можно узнать текущий issuer и настройки Authorization Server без прямого внедрения `AuthorizationServerSettings` в каждый компонент.


----
#### [[AuthorizationServerContext - Spring Security - Flashcards|Link to flashcards]]



---
### References:

