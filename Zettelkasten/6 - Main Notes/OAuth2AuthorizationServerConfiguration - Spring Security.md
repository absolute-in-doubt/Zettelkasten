
14-06-2026 08:14

Status:

Tags: [[Spring Security]] [[Spring]]

---
# OAuth2AuthorizationServerConfiguration - Spring Security




`OAuth2AuthorizationServerConfiguration` - это **~={purple}@Configuration=~** которая предоставляет **~={red}минимальную конфигурацию=~** для старта Oauth2 Auth server.

`OAuth2AuthorizationServerConfiguration` использует `OAuth2AuthorizationServerConfigurer`, который примененяет default конфигурацию и регистрируе [[Security Filter Chain - Spring Security|SecurityFilterChain]] `@Bean`.

При такой автоконфигурации автоматически регистрируются следующие эндпоинты:

- [OAuth2 Authorization endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-authorization-endpoint)
    
- [OAuth2 Token endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-token-endpoint)
    
- [OAuth2 Token Introspection endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-token-introspection-endpoint)
    
- [OAuth2 Token Revocation endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-token-revocation-endpoint)
    
- [OAuth2 Authorization Server Metadata endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-authorization-server-metadata-endpoint)
    
- [JWK Set endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-jwk-set-endpoint)

> [!note]
> **The JWK Set endpoint is configured **only** if a `JWKSource<SecurityContext>` `@Bean` is registered.**

> [!note]
> The following protocol endpoints are disabled by default:
>
>- [OAuth2 Device Authorization Endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-device-authorization-endpoint)
>    
>- [OAuth2 Device Verification Endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-device-verification-endpoint)
>    
>- [OAuth2 Client Registration endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-client-registration-endpoint)


The following example shows how to use `OAuth2AuthorizationServerConfiguration` to apply the minimal default configuration:

```java
@Configuration
@Import(OAuth2AuthorizationServerConfiguration.class)
public class AuthorizationServerConfig {

	@Bean
	public RegisteredClientRepository registeredClientRepository() {
		List<RegisteredClient> registrations = ...
		return new InMemoryRegisteredClientRepository(registrations);
	}

	@Bean
	public JWKSource<SecurityContext> jwkSource() {
		RSAKey rsaKey = ...
		JWKSet jwkSet = new JWKSet(rsaKey);
		return (jwkSelector, securityContext) -> jwkSelector.select(jwkSet);
	}

}
```

> [!warning]
> Если нужен `JwtDecoder`, то его удобно взять из `OAuth2AuthorizationServerConfiguration`:
>
>```java
>@Bean
>public JwtDecoder jwtDecoder(JWKSource<SecurityContext> jwkSource) {
>	return OAuth2AuthorizationServerConfiguration.jwtDecoder(jwkSource);
>}
>```





----
#### [[Oauth2-authorization-server configuration - Spring Security - Flashcards|Link to flashcards]]



---
### References:

