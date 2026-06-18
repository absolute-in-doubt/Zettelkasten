
12-02-2026 18:54

Status:

Tags: [[Spring Security]] [[Spring]]

---
# AuthenticationManager - Spring Security

Это интерфейс. Его реализация собирает в себе все реализации [[AuthenticationProvider - Spring Security|AuthenticationProvider]].

~={cyan}Чтобы зарегать свою реализацию AuthenticationProvider - регистрируем его как `@Component`.=~

```java
@FunctionalInterface  
public interface AuthenticationManager {  
    Authentication authenticate(Authentication authentication) throws AuthenticationException;  
}
```


##### Использование AuthenticationManager делает код чуть более универсальным:

Было:
```java
Authentication authToken = new BearerTokenAuthenticationToken(jwt);  
Authentication auth = JwtAuthenticationProvider.authenticate(authToken);
```

Стало:
```java
Authentication authToken = new BearerTokenAuthenticationToken(jwt);  
Authentication auth = authManager.authenticate(authToken);
```

AuthManager проверяет по цепочке каждый AuthenticationProvider, может ли он обработать полученный `Authentication authToken`:

Метод `JwtAuthenticationProvider`:
```java
public boolean supports(Class<?> authentication) {  
    return BearerTokenAuthenticationToken.class.isAssignableFrom(authentication);  
}
```

И когда находит провайдер, который может обработать это `authToken` - вызывает метод `authenticate` этого провайдера.


---
### Реализации AuthenticationProvider и Authenticaation, которые онии обрабатывают

- `JwtAuthenticationProvider` обрабатывает `BearerTokenAuthenticationToken`
  Проверяет валидность Jwt
  Для работы требует `JwtEncoder`, `JwtDecoder`, `JwtAuthenticationConverter` beans.

- `DaoAuthenticationProvider` обрабатывает `UsernamePasswordAuthenticationToken`
  Используется для аутентификации по логину и паролю (usecase: если jwt не валиден и надо обновить)
  Для работы требует реализацию [[UserDetailsService - Spring Security|UserDetailsService]] в контексте - ~={cyan}указываем её как `@Service`.=~


----
#### [[AuthenticationManager - Spring Security - Flashcards|Link to flashcards]]



---
### References:

