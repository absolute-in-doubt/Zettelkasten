
30-11-2025 13:11

Status:

Tags: [[Spring]]

---
# @RestController - Spring Web

Аналог [[@ResponseBody - Spring Web MVC|@ResponseBody]] но class-level. Обозначает, что все методы контроллера возвращают данные, (а не путь к представлению):

```java
@RestController
@RequestMapping("/api")
public class MyController {
	
	@GetMapping("/sayHello")
	public String sayHello() {
		return "Hello, world!";
	}
}
```

----
#### [[@RestController - Spring Web - Flashcards|Link to flashcards]]



---
### References:

