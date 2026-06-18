
13-06-2026 20:47

Status:

Tags: [[Spring Security]] [[Spring]]

---
# Oauth2Authorization - Spring Security Oauth2 Authorization Server


### `OAuth2Authorization`

Представляет выданную авторизацию.

Содержит:

- access token
- refresh token
- authorization code
- principal 
- scopes


По сути `OAuth2Authorization` = Вся OAuth-сессия


Пример:

```text
User
↓
Authorization Code
↓
Access Token
↓
Refresh Token
```

Все это хранится внутри одной сущности.



---
### `OAuth2AuthorizationService`

Хранилище авторизаций.

Аналоги:

```java
InMemoryOAuth2AuthorizationService

JdbcOAuth2AuthorizationService
```

Используется для:

- поиска access token;
- поиска refresh token;
- сохранения authorization code;  
- отзывов токенов.


----
#### [[Oauth2Authorization - Spring Security Oauth2 Authorization Service - Flashcards|Link to flashcards]]



---
### References:

- [[Oauth2-authorization-server overview - Spring Security]]