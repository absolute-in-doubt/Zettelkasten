
14-06-2026 08:30

Status:

Tags: [[Spring Security]] [[Spring]]

---
# OAuth2AuthorizationServerConfigurer - Spring Security


`OAuth2AuthorizationServerConfigurer` позволяет полностью кастомизировать security configuration для  Oauth2 authorization server.

Он позволяет определить главные компоненты, например:
- [[RegisteredClient - Spring Security Oauth2 Authorization Server#`RegisteredClientRepository`|RegisteredClientRespository]]
- [[Oauth2Authorization - Spring Security Oauth2 Authorization Server#`OAuth2AuthorizationService`|Oauth2AuthorizationService]]
- [[OAuth2-resource-server overview - Spring Security#Основные абстракции|Oauth2TokenGenerator]]
- ...


`OAuth2AuthorizationServerConfigurer` provides the following configuration options:

![[Pasted image 20260614083746.png]]

1. `registeredClientRepository()`: The [[RegisteredClient - Spring Security Oauth2 Authorization Server#`RegisteredClientRepository`|RegisteredClientRespository]] ~={orange}**REQUIRED**=~ for managing new and existing clients

2. `authorizationService()`: The [[Oauth2Authorization - Spring Security Oauth2 Authorization Server#`OAuth2AuthorizationService`|Oauth2AuthorizationService]] for managing new and existing authorizations.

3. `authorizationConsentService()`: The [[Oauth2AuthorizationConsent - Spring Security Oauth2 Authorization Server#`OAuth2AuthorizationConsentService`|OAuth2AuthorizationConsentService]] for managing new and existing authorization consents.

4. `authorizationServerSettings()`: The [[AuthorizationServerSettings - Spring Security|AuthorizationServerSettings]] ~={orange}**REQUIRED**=~ for customizing configuration settings for the OAuth2 authorization server.

5. `tokenGenerator()`: The [[OAuth2-resource-server overview - Spring Security#Основные абстракции|OAuth2TokenGenerator]] for generating tokens supported by the OAuth2 authorization server

6. `clientAuthentication()`: [[OAuth2ClientAuthenticationConfigurer - Spring Security|The configurer for OAuth2 Client Authentication]]

7.  `authorizationEndpoint()`: The configurer for the [OAuth2 Authorization endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-authorization-endpoint).

8. `pushedAuthorizationRequestEndpoint()`: The configurer for the [OAuth2 Pushed Authorization Request endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-pushed-authorization-request-endpoint).

9. `deviceAuthorizationEndpoint()`: The configurer for the [OAuth2 Device Authorization endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-device-authorization-endpoint).

10. `deviceVerificationEndpoint()`: The configurer for the [OAuth2 Device Verification endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-device-verification-endpoint)

11. `tokenEndpoint()`: The configurer for the [OAuth2 Token endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-token-endpoint).


12. `tokenIntrospectionEndpoint()`: The configurer for the [OAuth2 Token Introspection endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-token-introspection-endpoint).

13. `tokenRevocationEndpoint()`: The configurer for the [OAuth2 Token Revocation endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-token-revocation-endpoint).

14. `clientRegistrationEndpoint()`: The configurer for the [OAuth2 Client Registration endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-client-registration-endpoint)

15. `authorizationServerMetadataEndpoint()`: The configurer for the [OAuth2 Authorization Server Metadata endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-authorization-server-metadata-endpoint).

16. `authorizationServerMetadataEndpoint()`: The configurer for the [OAuth2 Authorization Server Metadata endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-authorization-server-metadata-endpoint).

17. `logoutEndpoint()`: The configurer for the [OpenID Connect 1.0 Logout endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oidc-logout-endpoint).

18. `userInfoEndpoint()`: The configurer for the [OpenID Connect 1.0 UserInfo endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oidc-user-info-endpoint).

19. `clientRegistrationEndpoint()`: The configurer for the [OpenID Connect 1.0 Client Registration endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oidc-client-registration-endpoint).

----
#### [[OAuth2AuthorizationServerConfigurer - Spring Security - Flashcards|Link to flashcards]]



---
### References:

