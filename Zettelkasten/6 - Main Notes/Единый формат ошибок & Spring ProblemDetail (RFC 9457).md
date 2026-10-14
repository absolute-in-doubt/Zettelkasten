
05-10-2026 18:20

Status:

Tags: [[Networks]] [[Spring Web]] [[Spring]] [[Java+]]

---
# Единый формат ошибок & Spring ProblemDetail (RFC 9457)


**ProblemDetail** — это стандартный формат представления ошибок HTTP API, определённый в **RFC 9457**. Он заменяет более старый RFC 7807. Идея простая: вместо того чтобы каждый REST-сервис придумывал свой JSON ошибки, использовать единый контракт. ([RFC Editor][1])

### 1. Зачем он нужен

Допустим, без стандарта разные сервисы могут возвращать:

```json
{
  "error": "USER_NOT_FOUND",
  "message": "User 123 not found"
}
```

или:

```json
{
  "code": 404,
  "errorMessage": "No such user"
}
```

или:

```json
{
  "errors": [
    {"field": "email", "message": "Invalid email"}
  ]
}
```

`ProblemDetail` предлагает общий базовый формат:

```json
{
  "type": "https://example.com/problems/user-not-found",
  "title": "User not found",
  "status": 404,
  "detail": "User with id 123 was not found",
  "instance": "/users/123"
}
```

Content-Type обычно:

```http
application/problem+json
```



---
## 2. Основные поля

У RFC есть пять стандартных полей:

| Поле       | Назначение                                 |
| ---------- | ------------------------------------------ |
| `type`     | URI, идентифицирующий **тип проблемы**     |
| `title`    | Краткое человекочитаемое название проблемы |
| `status`   | HTTP status code                           |
| `detail`   | Детальное описание конкретного случая      |
| `instance` | URI конкретного возникновения ошибки       |

Например:

```json
{
  "type": "https://api.mybank.ru/problems/insufficient-funds",
  "title": "Insufficient funds",
  "status": 422,
  "detail": "Not enough money to perform the transfer",
  "instance": "/transfers/8f31..."
}
```

Важный момент: **`type` — это идентификатор типа ошибки, а `instance` — конкретного случая этой ошибки.**

Например:

```text
type     = insufficient-funds
instance = конкретный неуспешный перевод №123
```

`detail` предназначен для человека/клиента и не должен использоваться клиентом как machine-readable код — для этого лучше использовать `type` или дополнительные поля. ([RFC Editor][1])



---
# 3. Что здесь делает Java / Spring

В современном Spring есть готовый класс:

```java
org.springframework.http.ProblemDetail
```

Он непосредственно представляет RFC 9457 Problem Detail. Spring также умеет автоматически сериализовать его в `application/problem+json`. 

Например:

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        throw new UserNotFoundException(id);
    }
}
```

Можно обработать исключение:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    ProblemDetail handle(UserNotFoundException ex) {

        ProblemDetail problem =
                ProblemDetail.forStatus(HttpStatus.NOT_FOUND);

        problem.setTitle("User not found");
        problem.setDetail(ex.getMessage());
        problem.setType(
                URI.create("https://api.example.com/problems/user-not-found")
        );

        return problem;
    }
}
```

Клиент получит примерно:

```http
HTTP/1.1 404 Not Found
Content-Type: application/problem+json
```

```json
{
  "type": "https://api.example.com/problems/user-not-found",
  "title": "User not found",
  "status": 404,
  "detail": "User 123 not found",
  "instance": "/users/123"
}
```

Spring может также автоматически установить `instance` из текущего URL, если он не был задан. 



---
# 4. Можно добавлять свои поля

Это одна из важных особенностей RFC.

Например, для validation error:

```json
{
  "type": "https://api.example.com/problems/validation-error",
  "title": "Validation failed",
  "status": 400,
  "detail": "Request contains invalid fields",
  "errors": [
    {
      "field": "email",
      "message": "Invalid email"
    },
    {
      "field": "age",
      "message": "Must be positive"
    }
  ]
}
```

`errors` здесь уже **не часть базового RFC**, а application-specific extension. RFC прямо допускает такие расширения. ([RFC Editor][1])

В Spring:

```java
ProblemDetail problem =
        ProblemDetail.forStatus(HttpStatus.BAD_REQUEST);

problem.setTitle("Validation failed");

problem.setProperty("errors", errors);
```

Spring развернёт `properties` в JSON как обычные поля верхнего уровня. ([Home][2])


---
# 5. Почему `type` особенно важен

Допустим, клиент получил:

```json
{
  "type": "https://api.bank.ru/problems/card-blocked",
  "title": "Card blocked",
  "status": 409,
  "detail": "The card is blocked because of suspicious activity"
}
```

Клиент может программно определить:

```text
type == card-blocked
    ↓
показать пользователю специальный UI
```

В отличие от:

```json
{
  "detail": "Your card was blocked because..."
}
```

где клиенту пришлось бы анализировать текст.

RFC рекомендует идентифицировать тип проблемы именно через `type`; URI может также вести на документацию этого типа ошибки. 



---
## 6. Как это обычно выглядит в Spring-проекте

В реальном backend я бы строил примерно так:

```text
Controller
    ↓
Service
    ↓
Domain Exception
    ↓
@RestControllerAdvice
    ↓
ProblemDetail
    ↓
HTTP 4xx/5xx
```

Например:

```java
public class UserAlreadyExistsException
        extends RuntimeException {

    public UserAlreadyExistsException(String email) {
        super("User with email " + email + " already exists");
    }
}
```

и:

```java
@ExceptionHandler(UserAlreadyExistsException.class)
ProblemDetail handle(UserAlreadyExistsException ex) {

    ProblemDetail problem =
            ProblemDetail.forStatus(HttpStatus.CONFLICT);

    problem.setType(
        URI.create("https://api.example.com/problems/user-exists")
    );

    problem.setTitle("User already exists");
    problem.setDetail(ex.getMessage());

    return problem;
}
```

Получаем единый контракт для всех REST-ошибок.


---
### 7. Что стоит запомнить для собеседования

**ProblemDetail / RFC 9457 — стандартный формат ошибок HTTP API.**

Минимально:

```text
type      → какой тип проблемы
title     → краткое название
status    → HTTP status
detail    → подробности конкретного случая
instance  → конкретное возникновение
```

Плюс можно добавлять свои поля:

```json
{
  "type": "...",
  "title": "...",
  "status": 400,
  "detail": "...",
  "errors": [...]
}
```

В Spring есть готовый:

```java
org.springframework.http.ProblemDetail
```

и его удобно возвращать из `@ExceptionHandler` / `@RestControllerAdvice`. Spring также предоставляет `ErrorResponse` и `ErrorResponseException` для более полноценной интеграции обработки HTTP-ошибок. ([Home][2])

**15 секунд на собеседовании:**

> *ProblemDetail — это стандарт RFC 9457 для единообразного представления ошибок REST API. Он использует `type`, `title`, `status`, `detail` и `instance`, сериализуется как `application/problem+json`. В Spring есть готовый `ProblemDetail`, который можно возвращать из `@ExceptionHandler`, а специфичные для приложения данные добавлять через properties.*

[1]: https://www.rfc-editor.org/rfc/rfc9457.html?utm_source=chatgpt.com "RFC 9457: Problem Details for HTTP APIs"
[2]: https://docs.spring.io/spring/reference/web/webflux/ann-rest-exceptions.html?utm_source=chatgpt.com "Error Responses :: Spring Framework"


----
#### [[Единый формат ошибок & Spring ProblemDetail (RFC 9457) - Flashcards|Link to flashcards]]



---
### References:

