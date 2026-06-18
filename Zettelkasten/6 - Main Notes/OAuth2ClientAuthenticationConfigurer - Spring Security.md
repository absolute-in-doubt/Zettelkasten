
14-06-2026 10:34

Status:

Tags: [[Spring Security]] [[Spring]]

---
# OAuth2ClientAuthenticationConfigurer - Spring Security


Предназначен для кастомизации Oauth2 client authentication. 
По сути он конфигурирует `OAuth2ClientAuthenticationFilter`.

It defines extension points that let you customize the pre-processing, main processing, and post-processing logic for client authentication requests.


`OAuth2ClientAuthenticationConfigurer` provides the following configuration options:

![[Pasted image 20260614103610.png]]

1. `authenticationConverter()`: Adds an `AuthenticationConverter` (_pre-processor_) used when attempting to extract client credentials from `HttpServletRequest` to an instance of `OAuth2ClientAuthenticationToken`.

2. `authenticationConverters()`: Sets the `Consumer` providing access to the `List` of default and (optionally) added `AuthenticationConverter`'s allowing the ability to add, remove, or customize a specific `AuthenticationConverter`

3. `authenticationProvider()`: Adds an `AuthenticationProvider` (_main processor_) used for authenticating the `OAuth2ClientAuthenticationToken`.

4. `authenticationProviders()`: Sets the `Consumer` providing access to the `List` of default and (optionally) added `AuthenticationProvider`'s allowing the ability to add, remove, or customize a specific `AuthenticationProvider`.

5. `authenticationSuccessHandler()`: The `AuthenticationSuccessHandler` (_post-processor_) used for handling a successful client authentication and associating the `OAuth2ClientAuthenticationToken` to the `SecurityContext`.

6. `errorResponseHandler()`: The `AuthenticationFailureHandler` (_post-processor_) used for handling a failed client authentication and returning the [`OAuth2Error` response](https://datatracker.ietf.org/doc/html/rfc6749#section-5.2).

> [!example] **~={red}Important!=~**
> `OAuth2ClientAuthenticationConfigurer` configures the `OAuth2ClientAuthenticationFilter` and registers it with the OAuth2 authorization server `SecurityFilterChain` `@Bean`.
> 
>`OAuth2ClientAuthenticationFilter` is the `Filter` that processes client authentication requests.


By default, client authentication is required for:
- [OAuth2 Token endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-token-endpoint), 
- -[OAuth2 Token Introspection endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-token-introspection-endpoint), 
- -[OAuth2 Token Revocation endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/authorization-server/protocol-endpoints.html#oauth2AuthorizationServer-oauth2-token-revocation-endpoint).  

The supported client authentication methods are:
- `client_secret_basic`, 
- `client_secret_post`, 
- `private_key_jwt`, 
- `client_secret_jwt`,
- `tls_client_auth`, 
- `self_signed_tls_client_auth`, 
- `none` (public clients).


### Default config:

`OAuth2ClientAuthenticationFilter` is configured with the following defaults:

- **`AuthenticationConverter`** — A `DelegatingAuthenticationConverter` composed of:
	- `JwtClientAssertionAuthenticationConverter`,
	- `X509ClientCertificateAuthenticationConverter`, 
	- `ClientSecretBasicAuthenticationConverter`, 
	- `ClientSecretPostAuthenticationConverter`, 
	- `PublicClientAuthenticationConverter`.
    
- **`AuthenticationManager`** — An [[AuthenticationManager - Spring Security|AuthenticationManager]] composed of:
	- `JwtClientAssertionAuthenticationProvider`, 
	- `X509ClientCertificateAuthenticationProvider`, 
	- `ClientSecretAuthenticationProvider`, 
	- `PublicClientAuthenticationProvider`.
    
- **`AuthenticationSuccessHandler`** — An internal implementation that associates the “authenticated” `OAuth2ClientAuthenticationToken` (current `Authentication`) to the `SecurityContext`.
    
- **`AuthenticationFailureHandler`** — An internal implementation that uses the `OAuth2Error` associated with the `OAuth2AuthenticationException` to return the OAuth2 error response.



----
#### [[OAuth2ClientAuthenticationConfigurer - Spring Security - Flashcards|Link to flashcards]]



---
### References:

