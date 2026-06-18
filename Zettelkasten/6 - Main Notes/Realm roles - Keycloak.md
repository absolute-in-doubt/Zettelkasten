
03-04-2026 21:38

Status:

Tags: [[Keycloak]] [[Java+]]

---
# Realm roles - Keycloak

Realm role - роль пользователя в Realm. (Не в рамках клиента, а в рамках всего Realm-a).

Может быть:

- назначена пользователю напрямую
- включена в другие роли (composite)
- проверена в любом клиенте, если токен содержит `realm_access.roles` ([[Client Scope - Keycloak#`roles`|realm_access.roles]])




---
### Composite roles

Роли, включающие в себя другие роли.

**Пример:**

У нас есть роли:
- `read-users`
- `edit-users`
- `delete-users`

Мы создаём роль `manage-users` и делаем её composite, включив в неё все три роли выше.


Теперь:
- При назначении `manage-users` пользователю, он автоматически получает `read-users`, `edit-users` и `delete-users`.
- Это упрощает управление: назначаешь одну роль - пользователь получает комплексный доступ.


---
### Default roles


#### `admin`

- Composite

Включает административные права - может управлять пользователями, клиентами, ролями и конфигурацией realm-a.


---
#### `create-realm`

- Non-composite

Позволяет пользователю создавать новые realms через Admin Console или REST API (только в Master realm)


---
#### `default-roles`

- Composite

Набор ролей, назначаемых по умолчанию всем пользователям master realm.

Включает в себя:
- `offline_access` (именно роль, не Client Scope)
- `uma_authorization`
- и т.д. (можно посмотреть внутри composite)


---
#### `offline_access`

- Non-Composite

Даёт возможность получить offline refresh tokens - живут дольше, не требуют активной сессии пользователя.


---
### `uma_authorization`

- Non-Composite

Позволяет использовать User-Managed Access (UMA) - механизм, при котором пользователь может делегировать доступ к своим ресурсам другим пользователям (используется при ресурсно-ориентированном доступе).


----
#### [[Realm roles - Keycloak - Flashcards|Link to flashcards]]



---
### References:

