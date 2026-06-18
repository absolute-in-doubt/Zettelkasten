
28-05-2026 20:17

Status:

Tags: [[Spring Security]] [[Spring]]

---
# Конфигурация JWKS на auth service - Spring Security


### **1. Подключение зависимостей**

```xml
<!-- Spring Boot Starter для OAuth2 Resource Server -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
</dependency>
<!-- Nimbus JOSE + JWT для работы с JWKS (идет в составе oauth2-resource-server)-->
<dependency>
    <groupId>com.nimbusds</groupId>
    <artifactId>nimbus-jose-jwt</artifactId>
    <version>9.31</version> <!-- или актуальная версия -->
</dependency>
```


---
### **2. Конфигурация JWKS в application.yml**

Пример хранения JWKS в виде **PEM-ключей** (или JSON) в `application.yml`:

```yaml
jwks:
  keys:
    - kid: "key-1"
      kty: "RSA"
      alg: "RS256"
      use: "sig"
      n: "modulus_base64"
      e: "AQAB"
      d: "private_exponent_base64" # только для тестов!
      p: "prime1_base64"
      q: "prime2_base64"
    # ... другие ключи
```

> **Важно:** В продакшене **не храните приватные ключи** в конфиге! Используйте **Vault**, **KMS** или внешние сервисы.


---
### **3. Чтение JWKS из конфигурации**

Создаём класс для маппинга JWKS из `application.yml`:

```java
@ConfigurationProperties(prefix = "jwks")
public record JwksProperties(List<JwkKey> keys) {
    public record JwkKey(
        String kid,
        String kty,
        String alg,
        String use,
        String n,
        String e,
        String d,
        String p,
        String q
    ) {}
}
```

Регистрируем в `@Configuration`:

```java
@EnableConfigurationProperties(JwksProperties.class)
@Configuration
public class JwksConfig {
    // ...
}
```


---
### **4. Создание JWKSet из конфигурации**

Бин для генерации `JWKSet` из свойств:

```java
@Bean
public JWKSet jwkSet(JwksProperties jwksProperties) {
    List<JWK> jwks = jwksProperties.keys().stream()
        .map(key -> {
            RSAKey.Builder builder = new RSAKey.Builder(
                new Base64URL(key.n()),
                new Base64URL(key.e())
            )
            .keyID(key.kid())
            .algorithm(JWSAlgorithm.parse(key.alg()))
            .keyUse(KeyUse.parse(key.use()));

            if (key.d() != null) {
                builder.privateExponent(new Base64URL(key.d()));
            }
            if (key.p() != null && key.q() != null) {
                builder.prime(new Base64URL(key.p()), new Base64URL(key.q()));
            }
            return builder.build();
        })
        .toList();
    return new JWKSet(jwks);
}
```


---
### **5. Настройка NimbusJwtEncoder и NimbusJwtDecoder**


### На auth service 

где JWKSet доступен в контексте
```java
@Bean  
public JWKSource<SecurityContext> jwkSource(JWKSet jwkSet) {  
    return (jwkSelector, securityContext) -> jwkSelector.select(jwkSet);  
}  
  
@Bean  
public JwtEncoder jwtEncoder(JWKSource<SecurityContext> jwkSource) {  
    return new NimbusJwtEncoder(jwkSource);  
}  
  
@Bean  
public JwtDecoder jwtDecoder(JWKSource<SecurityContext> jwkSource) {  
    return NimbusJwtDecoder  
            .withJwkSource(jwkSource)  
            .build();  
}
```


---
### **6. Подъём JWKS Endpoint**

Стандартный эндпоинт `/jwks` поднимается автоматически, если:

- В проекте есть **Spring Security OAuth2 Resource Server**.
- Есть бин `JWKSource<SecurityContext>` (например, на основе `JWKSet`).

Пример кастомного эндпоинта (если нужно):

```java
@RestController
@RequestMapping("/jwks")
public class JwksController {
    private final JWKSet jwkSet;

    public JwksController(JWKSet jwkSet) {
        this.jwkSet = jwkSet;
    }

    @GetMapping
    public Map<String, Object> jwks() {
        return jwkSet.toJSONObject();
    }
}
```


---
### **7. Конфигурация Resource Server**

```java
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth.anyRequest().authenticated())
            .oauth2ResourceServer(oauth2 -> oauth2
                .jwt(jwt -> jwt.decoder(jwtDecoder(null))) // используем наш JwtDecoder
            );
        return http.build();
    }
}
```


---
### **8. Проверка работы**

- Отправьте запрос на `/jwks` — должен вернуть JSON с публичными ключами.
- Проверьте валидацию JWT:
  ```bash
  curl -H "Authorization: Bearer <your_jwt>" http://localhost:8080/protected
  ```


---
### **9. Дополнительные советы**

- **Ротация ключей:** Для продакшена используйте **Key Rotation** (например, через `JWKSource` с кэшированием).
- **Безопасность:** Никогда не expose приватные ключи в `/jwks`!
- **Валидация:** Настройте `JwtValidator` для проверки `iss`, `aud`, `exp` и т.д.
- **Nimbus:** Используйте `com.nimbusds:nimbus-jose-jwt` для работы с JWK/JWT.


---
### **10. Полезные ссылки**

- [Spring Security OAuth2 Resource Server Docs](https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/index.html)
- [Nimbus JOSE + JWT](https://connect2id.com/products/nimbus-jose-jwt)
- [JWK RFC 7517](https://datatracker.ietf.org/doc/html/rfc7517)

----
#### [[Конфигурация JWKS на auth service - Spring Security - Flashcards|Link to flashcards]]



---
### References:

