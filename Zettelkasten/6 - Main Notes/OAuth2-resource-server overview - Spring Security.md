
05-04-2026 08:14

Status:

Tags: [[Spring Security]] [[Spring]]

---
# OAuth2-resource-server overview - Spring Security


### Основные абстракции

1. **`spring.security.oauth2.resource-server` (yaml/properties)**
    
    - Конфигурационный слой Spring Boot: через `issuer-uri`, `jwk-set-uri`, `opaqueToken` и т.п. автоматически собирается цепочка OAuth 2.0‑фильтров и декодеров.
        
    - Внутри порождает бины `JwtDecoder`, `OpaqueTokenIntrospector`, `AuthenticationManager` и фильтры для `SecurityFilterChain`.
        
2. **`JwtDecoder` / `OpaqueTokenIntrospector`**
    
    - `JwtDecoder` — отвечает за **валидацию JWT‑токена** (подпись, время жизни, issuer и т.д.).
        
    - `OpaqueTokenIntrospector` — выполняет **introspection‑запрос** к серверу авторизации, чтобы проверить opaque‑токен (обычно `introspect` endpoint).habr+1
        
    - Эти интерфейсы задают, _как именно_ токен проверяется; Spring Boot автоматически создаёт их реализации по `issuer-uri` или `jwk-set-uri`.
        
3. **`BearerTokenAuthenticationFilter`**
    
    - Это **ключевой фильтр** в сценарии OAuth 2.0 Resource Server: он перехватывает HTTP‑запросы с `Authorization: Bearer <token>`, извлекает токен и передаёт его в `AuthenticationManager` (через `AuthenticationProvider`).
        
    - Если всё проходит удачно, создаётся `Authentication`/`OAuth2AuthenticatedPrincipal` (обычно `JwtAuthentication` или `BearerTokenAuthentication`) и кладётся в `SecurityContext` — дальше уже работает `AuthorizationFilter` из `authorizeHttpRequests`.
        

---
### References:

