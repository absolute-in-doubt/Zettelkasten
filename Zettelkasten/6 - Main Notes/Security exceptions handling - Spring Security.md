
04-06-2026 15:27

Status:

Tags: [[Spring Security]] [[Spring]]

---
# Security exceptions handling - Spring Security


### Spring native authentication exceptions


```java
throw new BadCredentialsException("Invalid JWT");

throw new InsufficientAuthenticationException("JWT is missing");
```


---
# Но чаще делают ещё проще

В JWT-фильтре обычно не выбрасывают исключение наружу, а вызывают `AuthenticationEntryPoint`.

Например:

```java
try {
    Authentication auth = jwtAuthenticationProvider.authenticate(token);
    SecurityContextHolder.getContext().setAuthentication(auth);
    filterChain.doFilter(request, response);
} catch (AuthenticationException ex) {
    authenticationEntryPoint.commence(request, response, ex);
}
```

Это особенно удобно для `OncePerRequestFilter`.

`AuthenticationEntryPoint` — это компонент Spring Security, который определяет:

> **Как ответить клиенту, если аутентификация не удалась или отсутствует.**

Обычно формирует ответ **401 Unauthorized**.



---

# Где Spring Security ловит исключения

За это отвечает `ExceptionTranslationFilter`.

Он ловит:

```java
AuthenticationException
```

и вызывает:

```java
AuthenticationEntryPoint
```

---

Также он ловит:

```java
AccessDeniedException
```

и вызывает:

```java
AccessDeniedHandler
```

---

Схема выглядит так:

```text
JwtAuthenticationFilter
        ↓
AuthenticationException
        ↓
ExceptionTranslationFilter
        ↓
AuthenticationEntryPoint
        ↓
401 Unauthorized
```

---

# Настройка обработки

```java
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    return http
        .exceptionHandling(ex -> ex
            .authenticationEntryPoint(jwtAuthenticationEntryPoint)
            .accessDeniedHandler(jwtAccessDeniedHandler)
        )
        .build();
}
```

---

# AuthenticationException vs AccessDeniedException

Очень частая путаница.

### AuthenticationException → 401

Пользователь не установлен:

- JWT отсутствует
    
- JWT просрочен
    
- JWT повреждён
    
- подпись невалидна
    

```http
401 Unauthorized
```

---

### AccessDeniedException → 403

Пользователь установлен, но прав недостаточно:

```java
@PreAuthorize("hasRole('ADMIN')")
```

JWT валиден, но роль USER.

```http
403 Forbidden
```

---

# Если JWT просрочен

Обычно:

```java
catch (JwtException ex) {
    throw new BadCredentialsException("Invalid token", ex);
}
```

или

```java
throw new CredentialsExpiredException("Token expired");
```

`CredentialsExpiredException` уже наследуется от `AuthenticationException`.

---

# Важный нюанс для кастомного фильтра

Если твой фильтр стоит **до `ExceptionTranslationFilter`**, то брошенный `AuthenticationException` может вообще никем не быть пойман.

Поэтому для `OncePerRequestFilter` часто рекомендуют:

```java
try {
    ...
} catch (AuthenticationException ex) {
    SecurityContextHolder.clearContext();
    authenticationEntryPoint.commence(request, response, ex);
    return;
}
```

а не рассчитывать на глобальную обработку.

---

# Практическая рекомендация

Для JWT-фильтра:

```java
try {
    ...
} catch (ExpiredJwtException ex) {
    authenticationEntryPoint.commence(
        request,
        response,
        new CredentialsExpiredException("JWT expired", ex)
    );
    return;
} catch (JwtException ex) {
    authenticationEntryPoint.commence(
        request,
        response,
        new BadCredentialsException("Invalid JWT", ex)
    );
    return;
}
```

А ответы `401` и тело ошибки формировать в собственном `AuthenticationEntryPoint`.

Это наиболее типичный подход для Spring Security-приложений.

----
#### [[Security exceptions handling - Spring Security - Flashcards|Link to flashcards]]



---
### References:

