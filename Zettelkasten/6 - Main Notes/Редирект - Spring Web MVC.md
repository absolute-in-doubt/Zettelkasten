
02-12-2025 15:06

Status:

Tags: [[Spring]]

---
# Редирект - Spring Web MVC

### Возврат `RedirectView`

```java
@RestController
public class MyRestController {

    @GetMapping("/process")
    public RedirectView process(@RequestParam boolean success) {
        if (success) {
            return new RedirectView("/success");
        } else {
            return new RedirectView("/error");
        }
    }
}
```

- Здесь возвращается объект `RedirectView`, и Spring MVC сам сформирует HTTP‑ответ с редиректом.
    

### 2. Возврат `ResponseEntity` с заголовком `Location`


```java
@RestController
public class MyRestController {

    @GetMapping("/process")
    public ResponseEntity<Void> process(@RequestParam boolean success) {
        if (success) {
            return ResponseEntity.status(HttpStatus.FOUND) // 302
                                 .location(URI.create("/success"))
                                 .build();
        } else {
            return ResponseEntity.status(HttpStatus.FOUND)
                                 .location(URI.create("/error"))
                                 .build();
        }
    }
}
```

- Это более «REST‑подход»: ты явно указываешь статус и заголовок `Location`.
    

### 3. Использовать `HttpServletResponse`

```java
@RestController
public class MyRestController {

    @GetMapping("/process")
    public void process(@RequestParam boolean success, HttpServletResponse response) throws IOException {
        if (success) {
            response.sendRedirect("/success");
        } else {
            response.sendRedirect("/error");
        }
    }
}
```

- Низкоуровневый способ, напрямую через сервлет API.

----
#### [[Редирект - Spring Web MVC - Flashcards|Link to flashcards]]



---
### References:

