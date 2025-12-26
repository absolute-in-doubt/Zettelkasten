
03-12-2025 15:33

Status:

Tags: [[Spring]]

---
# Работа с JWT - Spring Security

## Основные классы

- `JwtEncoder` — интерфейс для формирования JWT.
	
	- Реализация: `NimbusJwtEncoder`.
	
	- Принимает `JwtClaimsSet` (набор claims: subject, issuedAt, expiresAt, custom поля).

- `JwtDecoder` — интерфейс для валидации и разбора JWT.
    
    - Реализация: `NimbusJwtDecoder`.
	
    - Проверяет подпись и извлекает claims.

- `JwtClaimsSet` — объект, содержащий данные, которые будут включены в JWT (аналогично тому, как `UserDetails` хранит данные пользователя, но для токена).

- `Jwt` — результат декодирования: содержит заголовки и claims токена.
   

## ✅ Пример: Создание JWT

```java
import org.springframework.security.oauth2.jwt.*;

import java.time.Instant;
import java.time.temporal.ChronoUnit;

public class JwtService {

    private final JwtEncoder jwtEncoder;

    public JwtService(JwtEncoder jwtEncoder) {
        this.jwtEncoder = jwtEncoder;
    }

    public String generateToken(String username) {
        Instant now = Instant.now();

        JwtClaimsSet claims = JwtClaimsSet.builder()
                .issuer("my-app")
                .issuedAt(now)
                .expiresAt(now.plus(1, ChronoUnit.HOURS))
                .subject(username)
                .claim("role", "USER")
                .build();

        return jwtEncoder.encode(JwtEncoderParameters.from(claims)).getTokenValue();
    }
}
```

- Здесь мы формируем claims вручную.
   
- `JwtEncoder` подписывает токен и возвращает строку.


## ✅ Пример: Валидация JWT

```java
import org.springframework.security.oauth2.jwt.*;

public class JwtValidator {

    private final JwtDecoder jwtDecoder;

    public JwtValidator(JwtDecoder jwtDecoder) {
        this.jwtDecoder = jwtDecoder;
    }

    public Jwt decodeToken(String token) {
        return jwtDecoder.decode(token);
    }
}
```

- `JwtDecoder` проверяет подпись и возвращает объект `Jwt`.
   
- Из него можно получить claims:


```java
Jwt jwt = jwtDecoder.decode(token);
String subject = jwt.getSubject();
String role = jwt.getClaim("role");
```

## 🧭 Итог

- Для **создания JWT** используется `JwtEncoder` + `JwtClaimsSet`.

- Для **валидации JWT** используется `JwtDecoder`.
   
- Нет аналога `UserDetails` для JWT — claims формируются вручную.
   
- После проверки токена claims можно положить в `SecurityContextHolder` и использовать в контроллерах через `Principal` или `@AuthenticationPrincipal`.


----
#### [[Работа с JWT - Spring Security - Flashcards|Link to flashcards]]



---
### References:

