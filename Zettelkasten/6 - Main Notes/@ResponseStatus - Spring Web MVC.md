
07-01-2026 12:30

Status:

Tags: [[Spring]]

---
# @ResponseStatus - Spring Web MVC

`@ResponseStatus` — это аннотация Spring MVC, которой помечают **метод контроллера или класс исключения**, чтобы задать HTTP‑статус (и при необходимости текст причины) для ответа.

- Аннотация из пакета: `org.springframework.web.bind.annotation.ResponseStatus`.​
    
- Можно ставить:
    
    - на метод контроллера;
        
    - на метод‑обработчик исключений (`@ExceptionHandler`);
        
    - на класс исключения.


### На методе контроллера

Здесь по умолчанию вместо 200 будет отправлен статус 201 Created.

```java
@PostMapping("/orders")
@ResponseStatus(HttpStatus.CREATED) // 201
public void createOrder(@RequestBody OrderDto dto) {
    // создать заказ
}
```

Можно добавить reason:

```java
@ResponseStatus(code = HttpStatus.BAD_REQUEST, reason = "Invalid parameters")
@GetMapping("/demo")
public void demo() { }
```

В этом случае Spring вызовет `sendError(status, reason)`, что чаще подходит для HTML‑страниц ошибок, а для REST‑API обычно используют `ResponseEntity` или `ResponseStatusException`.


### На классе исключения

```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class UserNotFoundException extends RuntimeException { }
```

при пробрасывании этого исключения из контроллера Spring вернёт статус 404, даже если метод сам по себе завершился с исключением.


### В связке с обработчиками ошибок

`@ResponseStatus` также используют:​

- в методах с `@ExceptionHandler` внутри контроллера или `@ControllerAdvice`;

- чтобы каждый такой обработчик возвращал нужный статус, не создавая вручную `ResponseEntity`.

----
#### [[@ResponseStatus - Spring Web MVC - Flashcards|Link to flashcards]]



---
### References:

