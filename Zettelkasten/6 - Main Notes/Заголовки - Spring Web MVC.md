
02-12-2025 15:34

Status:

Tags: [[Spring Web MVC]] [[Spring]]

---
# Чтение заголовков запроса


### Через аннотацию `@RequestHeader`


```java
@RestController
public class HeaderController {

    @GetMapping("/hello")
    public String hello(@RequestHeader("User-Agent") String userAgent) {
        return "Ваш User-Agent: " + userAgent;
    }
}
```

- Можно указать `required=false` и `defaultValue`:

```java
@RequestHeader(value="X-Custom", required=false, defaultValue="none")
```

### 2. Через объект `HttpServletRequest`

```java
@GetMapping("/hello")
public String hello(HttpServletRequest request) {
    String userAgent = request.getHeader("User-Agent");
    return "UA: " + userAgent;
}
```

- Низкоуровневый способ, напрямую через Servlet API.


### 3. Через `@RequestHeader Map<String, String>`

```java
@GetMapping("/headers")
public Map<String, String> headers(@RequestHeader Map<String, String> headers) {
    return headers;
}
```

- Позволяет получить все заголовки сразу.



---
# Установка заголовков ответа

### 1. Через `ResponseEntity`

```java
@GetMapping("/download")
public ResponseEntity<String> download() {
    return ResponseEntity.ok()
            .header("Content-Disposition", "attachment; filename=data.txt")
            .body("file content");
}
```

- Самый удобный способ: полный контроль над статусом, заголовками и телом.


### 2. Через `HttpServletResponse`

```java
@GetMapping("/download")
public void download(HttpServletResponse response) throws IOException {
    response.setHeader("Content-Disposition", "attachment; filename=data.txt");
    response.getWriter().write("file content");
}
```

- Низкоуровневый способ, напрямую через Servlet API.


### 3. Через `@ResponseHeader` (косвенно)

В Spring нет отдельной аннотации для установки заголовков, но можно использовать:

- `ResponseEntity` (рекомендуется),
    
- или `@ControllerAdvice` + `ResponseBodyAdvice` для глобальной модификации заголовков.


----
#### [[Заголовки - Spring Web MVC - Flashcards|Link to flashcards]]



---
### References:

