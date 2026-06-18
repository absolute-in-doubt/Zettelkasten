
04-04-2026 15:33

Status:

Tags: [[Keycloak]] [[Java+]]

---
# Grant Types - Keycloak


Grant Type - это способ, которым клиент получает Access Token от сервера авторизации в [[OAuth 2.0 - System Design|OAuth 2.0]] / [[OpenID Connect - System Design|OpenID Connect]].

Он определяет, при каких условиях клиент получает токены.

Keycloak как реализация Identity Provider и Authorization Server поддерживает разные grant types:
- Authorization Code
- PKCE
- Client Credentials
- Device Code
- Refresh Token

Устаревшие:
- Implicit Flow
- Password Grant

---

![[Pasted image 20260404155058.png]]

> [!tip] Note 
> Микросервисы обращаются к JWKS endpoint обычно только один раз - при старте приложения 

---
### Authorization Flow

Стандартный безопасный flow для веб-приложений, где сервер клиента может хранить секреты (браузер).


Пример:
	web-app (backend + frontend).

![[Pasted image 20260404155539.png]]

Клиент формирует POST запрос к token-endpoint авторизационного сервера с обязательными параметрами:
- `grant_type`: `Authorization Code`
- `authorization_code`: `<код_полученный_на_шаге_5>`
- `redirect_uri`: `<тот_же_URI_что_использовался_при_авторизации>`
- `client_id`: `<id_клиента>`
- `client_secret` `<секрет_клиента>`

---
### Authorization Code + PKCE

PKCE - Proof Key for Code Exchange.

Это модификация Authorization Code Flow для публичных клиентов (например, мобильных приложений или SPA - Single Page Applications), чтобы предотвратить атаку перехвата кода.

~={orange}**Для приложений, которые не могут хранить у себя секреты**=~

Клиент перед началом авторизации:
- Генерирует случайный код (`code_verifier`)
- Строит на его основе `code_challenge` (обычно SHA-256 hash)
- При первом запросе отправляется `code_challenge`
- При обмене Authorization Code на Access Token тправляется `code_verifier`.
- Authorization Server сверяет `code_challenge` и `code_verifier`:
	- Если всё сходится, только тогда выдаёт Access Token.

Это защищает от атки перехвата кода, т.к. даже если злоумышленник украдёт Authorization Code, без правильного `code_verifier` он не сможет обменять его на Access Token.

![[Pasted image 20260404161621.png|404]]

![[Pasted image 20260404161745.png]]


---
### Client Credentials Flow

![[Pasted image 20260404154103.png]]

1. Сервис делает запрос к Keycloak (передаёт `client_id` и `client_secret`)
2. Keycloak проверяет их
3. Возвращает Access Token (если все ок)
4. Сервис делает запрос к API другого сервиса (передавая Access Token)

---
### Refresh Token Flow ([[JWT - System Design|JWT]])

После получения Access Token можно получить новый токен без повторной аутентификаии пользователя.

Используется любыми клиентами с долгоживущими сессиями.

![[Pasted image 20260404154650.png]]





----
#### [[Grant Types - Keycloak - Flashcards|Link to flashcards]]



---
### References:

