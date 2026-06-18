
03-04-2026 20:54

Status:

Tags: [[Keycloak]] [[Java+]]

---
# Client Scope - Keycloak

Client Scope - механизм, определяющий, какие данные и разрешения получит клиент в Access token / ID token при аутентификации пользователя

Scope - способ контролировать, что именно получит клиент при запросе авторизации.


##### Assigned type:

![[Pasted image 20260403212052.png]]

- Default - этот scope всегда включается в токен по умолчанию, даже если клиент не запрашивает его явно;

- Optional - scope будет применён только если клиент его запросит в параметре `scope=...` при авторизации;

- None (Unassigned) - scope не применяется к клиенту вообще.


---
### Default client scopes


#### `acr`

- Default
- OpenID Connect

Добавляет в токен acr (Authentication Context Class Reference) - уровень аутентификации (например, MFA, пароль и т.д.)


---
#### `address`

- Optional
- OpenID Connect

Добавляет адрес пользователя (`address` claim) в токен (если заполнено в профиле)


---
#### `basic`

- Default
- OpenID Connect

Включает базовые claims:
- `sub`
- `name`
- `preferred_username`
- `given_name`
- `family_name`


---
#### `email`

- Default
- OpenID Connect

Добавляет `email` и `email_verified` claims в токены. (В `email_verified` передаём email только после того, как пользователь подтвердил его )


---
### `microprofile-jwt`


- Optional
- OpenID Connect

Обеспечивает совместимость с Eclipse MicroProfile JWT - популярным стандартом для Java microservices.


---
#### `offline_access`

- Optional
- OpenId Connect

Разрешает выдачу offline refresh tokent (живёт долго и используется без пользовательского взаимодействия).


---
#### `phone`

- Optional
- OpenID Connect

Добавляет `phone_number` и `phone_number_verified` в токен (если указано в профиле).


---
#### `profile`

- Default
- OpnID Connect

Добавляет в токен следующие claims:
- `name`
- `given_name`
- `family_name`
- `middle_name`
- `nickname`
- `preferred_username`
- `profile`
- `picture`
- `website`
- `gender`
- `birthdate`
- `zoneinfo`
- `locale`
- `updated_at`


---
#### `roles`

- Default
- OpenID Connect

Добавляет роли пользователя ([[Realm roles - Keycloak|realm roles]] и client roles) в токен:
- `realm_access.roles`
- `resource_access`

---
#### `role_list`

- Default
- SAML - ~={orange}**используется при протоколе SAML**=~

Включает список ролей (roles) пользователя в SAML assertions.

----
#### [[Client Scope - Keycloak - Flashcards|Link to flashcards]]



---
### References:

