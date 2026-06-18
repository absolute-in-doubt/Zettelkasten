
Index: [[Spring]]

- [[Авторизация vs Аутентификация]]
- [[Spring Security Filters]]
- [[AuthenticationManager - Spring Security]]
	- [[AuthenticationProvider - Spring Security]]
- [[UserDetailsService - Spring Security]]
	- [[Автоконфигурация UserDetailsService - Spring Security]]
- [[Авторизация - Spring Security]]
	- [[@PreAuthorize vs @Secured vs @RolesAllowed - Spring Security]]

- [[Authentication class - Spring Security]]
	- [[@AuthenticationPrincipal - Инъекция Authentication (UserDetails) в контроллере - Spring Security]]

HttpSession vs Cookie [[HttpSession vs Cookie - Java servlet API]]

- [[config - Spring Security]]

- [[Filter - jakarta.servlet]]
- [[FilterChain - jakarta.servlet]] 
	- [[Регистрация фильтров в FilterChain - jakarta.servlet]]
	- [[Регистрация фильтров в FilterChain - Spring]]
- DelegatingFilterProxy
- 
- [[Security Filter Chain - Spring Security]]
- [[Custom Security Filter - Spring Security]]
	- [[Adding Custom Security FIlter - Spring Security]]

- [[Security exceptions handling - Spring Security]]


|Зависимость|Назначение|
|---|---|
|`spring-boot-starter-oauth2-resource-server`|Проверять чужие JWT|
|`spring-security-oauth2-authorization-server`|Выдавать свои JWT, публиковать JWKS, OAuth2/OIDC endpoints|
|`spring-boot-starter-oauth2-client`|Логин через Google, GitHub, Keycloak и т.д.|

---
##### `spring.security.oauth2.resource-server

- [[OAuth2-resource-server overview - Spring Security]]

- [[Настройка подключения к Keycloak - Spring Security]]

- [[Работа с JWT - Spring Security]]
	- [[Конфигурация JWKS на auth service - Spring Security]]
		- [[Компоненты JWKS ключа - Spring Security]]



---

##### `spring-security-oauth2-authorization-server`

- [[Oauth2-authorization-server overview - Spring Security]]

Entities & model:
- [[RegisteredClient - Spring Security Oauth2 Authorization Server]]
- [[Oauth2Authorization - Spring Security Oauth2 Authorization Server]]
- [[Oauth2AuthorizationConsent - Spring Security Oauth2 Authorization Server]]



- [[AuthorizationServerContext - Spring Security]]

Configuration:
- [[OAuth2AuthorizationServerConfiguration - Spring Security]] - минимальный config
	- [[OAuth2AuthorizationServerConfigurer - Spring Security]] - sets up SecurityFilterChain
	- [[AuthorizationServerSettings - Spring Security]] - настройка эндпоинтов
	
	- [[OAuth2ClientAuthenticationConfigurer - Spring Security]]


---
### [[CORS]]