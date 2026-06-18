
14-06-2026 10:18

Status:

Tags: [[Spring Security]] [[Spring]]

---
# AuthorizationServerSettings - Spring Security


Объект `AuthorizationServerSettings` хранится в  [[AuthorizationServerContext - Spring Security|AuthorizationServerContext]], который по смыслу аналогичен `SecurityContext`, только хранит не информацию о пользователе, а информацию о самом Authorization Server.


Позволяет устанавливать URI для authorization endpoints и issuer.

Default `AuthorizationServerSetttings` configuration looks like this:

```java
public final class AuthorizationServerSettings extends AbstractSettings {
	
	...
	
	public static Builder builder() {
		return new Builder()
			.authorizationEndpoint("/oauth2/authorize")
			.pushedAuthorizationRequestEndpoint("/oauth2/par")
			.deviceAuthorizationEndpoint("/oauth2/device_authorization")
			.deviceVerificationEndpoint("/oauth2/device_verification")
			.tokenEndpoint("/oauth2/token")
			.tokenIntrospectionEndpoint("/oauth2/introspect")
			.tokenRevocationEndpoint("/oauth2/revoke")
			.clientRegistrationEndpoint("/oauth2/register")
			.jwkSetEndpoint("/oauth2/jwks")
			.oidcLogoutEndpoint("/connect/logout")
			.oidcUserInfoEndpoint("/userinfo")
			.oidcClientRegistrationEndpoint("/connect/register");
	}
	
	...
	
}
```

> [!note]
> `AuthorizationServerSettings` is a **~={orange}REQUIRED=~** component.

> [!tip]
> `@Import(`[[OAuth2AuthorizationServerConfiguration - Spring Security|OAuth2AuthorizationServerConfiguration.class]]`)` automatically registers an `AuthorizationServerSettings` `@Bean`, if not already provided.


Пример кастомизации:

```java
@Bean
public AuthorizationServerSettings authorizationServerSettings() {
	return AuthorizationServerSettings.builder()
		.issuer("https://example.com")
		.authorizationEndpoint("/oauth2/v1/authorize")
		.pushedAuthorizationRequestEndpoint("/oauth2/v1/par")
		.deviceAuthorizationEndpoint("/oauth2/v1/device_authorization")
		.deviceVerificationEndpoint("/oauth2/v1/device_verification")
		.tokenEndpoint("/oauth2/v1/token")
		.tokenIntrospectionEndpoint("/oauth2/v1/introspect")
		.tokenRevocationEndpoint("/oauth2/v1/revoke")
		.clientRegistrationEndpoint("/oauth2/v1/register")
		.jwkSetEndpoint("/oauth2/v1/jwks")
		.oidcLogoutEndpoint("/connect/v1/logout")
		.oidcUserInfoEndpoint("/connect/v1/userinfo")
		.oidcClientRegistrationEndpoint("/connect/v1/register")
		.build();
}
```


> [!note]
> Если не указать `issuer()` в `AuthorizationServerSettings`, то Spring пытается вычислить его из текущего HTTP-запроса.
> Например, пришел запрос:
>
>```
>GET https://auth.company.com/.well-known/openid-configuration
>```
>
>Тогда issuer будет определен как:
>
>```
>https://auth.company.com
>```

----
#### [[AuthorizationServerSettings - Spring Security - Flashcards|Link to flashcards]]



---
### References:

