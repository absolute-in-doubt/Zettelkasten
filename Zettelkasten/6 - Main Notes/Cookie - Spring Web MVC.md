
02-12-2025 15:50

Status:

Tags: [[Spring Web MVC]] [[Spring]]

---
# Cookie - Spring Web MVC

## Чтение Cookie

### 1. Через аннотацию `@CookieValue`

```java
@RestController
public class CookieController {

    @GetMapping("/profile")
    public String profile(@CookieValue(value = "sessionId", defaultValue = "none") String sessionId) {
        return "Ваш sessionId: " + sessionId;
    }
}
```

- Можно указать `required=false` и `defaultValue`.
    

### 2. Через `HttpServletRequest`

```java
@GetMapping("/profile")
public String profile(HttpServletRequest request) {
    Cookie[] cookies = request.getCookies();
    if (cookies != null) {
        for (Cookie cookie : cookies) {
            if ("sessionId".equals(cookie.getName())) {
                return "Ваш sessionId: " + cookie.getValue();
            }
        }
    }
    return "Cookie не найден";
}
```

- Низкоуровневый доступ ко всем кукам.
    

---
##  Установка Cookie

### 1. Через `HttpServletResponse`

```java
@GetMapping("/set-cookie")
public String setCookie(HttpServletResponse response) {
    Cookie cookie = new Cookie("sessionId", "abc123");
    cookie.setHttpOnly(true);
    cookie.setSecure(true);
    cookie.setPath("/");
    cookie.setMaxAge(3600); // 1 час
    response.addCookie(cookie);
    return "Cookie установлена";
}
```

- Полный контроль: `HttpOnly`, `Secure`, `MaxAge`, `Path`.
    

### 2. Через заголовки в `ResponseEntity`

```java
@GetMapping("/set-cookie")
public ResponseEntity<String> setCookie() {
    return ResponseEntity.ok()
            .header(HttpHeaders.SET_COOKIE, "sessionId=abc123; Path=/; HttpOnly; Secure")
            .body("Cookie установлена");
}
```

- Можно формировать заголовок `Set-Cookie` вручную.

> [!warning] Установка Cookie на сервере - **только** через заголовки
> Cookie устанавливаются только на клиенте/ Чтобы установить Cookie, его надо отправлять в заголовке Set-Cookie

----
#### [[Cookie - Spring Web MVC - Flashcards|Link to flashcards]]



---
### References:

