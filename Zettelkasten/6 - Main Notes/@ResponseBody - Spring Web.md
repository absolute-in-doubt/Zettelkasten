
30-11-2025 13:04

Status:

Tags: [[Spring]]

---
# @ResponseBody - Spring Web

Обозначает, что метод контроллера возвращает не шаблон (путь к странице), а тело ответа:

```java
@Controller
@RequestMapping("/api")
public class MyController {
	
	@ResponseBody
	@GetMapping("/sayHello")
	public String sayHello() {
		return "Hello, world!";
	}
}
```

> [!note]
> Если в методе контроллера, аннотированном `@ResponseBody` возвращать объект, то он будетавтоматически конвертирован в json с помощью Jackson:
> ```java
> @ResponseBody
>@GetMapping("/sayHello")
>public String sayHello() {
>	return Map.of("code", "200", "errorMessage", "");
>}
> ```

----
#### [[@ResponseBody - Spring Web - Flashcards|Link to flashcards]]



---
### References:

- [[@RestController - Spring Web]]