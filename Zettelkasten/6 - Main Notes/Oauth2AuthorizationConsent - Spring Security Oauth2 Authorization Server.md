
13-06-2026 20:48

Status:

Tags: [[Spring Security]] [[Spring]]

---
# Oauth2AuthorizationConsent - Spring Security Oauth2 Authorization Server


### `OAuth2AuthorizationConsent`

Хранит пользовательское согласие.

Например:

```text
Client:
    read_profile
    read_email

User:
    разрешил только read_profile
```

Хранится отдельно.



---

### `OAuth2AuthorizationConsentService`

Хранилище consent'ов.

Варианты:

```java
InMemoryOAuth2AuthorizationConsentService

JdbcOAuth2AuthorizationConsentService
```




----
#### [[Oauth2AuthorizationConsent - Spring Security Oauth2 Authorization Service - Flashcards|Link to flashcards]]



---
### References:

- [[Oauth2-authorization-server overview - Spring Security]]