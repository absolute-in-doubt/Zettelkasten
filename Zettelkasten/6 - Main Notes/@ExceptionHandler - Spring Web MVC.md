
30-11-2025 13:25

Status:

Tags: [[Spring]]

---
# @ExceptionHandler - Spring Web

Аннотирует метод контроллера, который вызывается при выбрасывании исключения в методах контроллера:

```java
@RestController
@RequestMapping("/people")
public class PeopleController {
	private final PeopleService service;
	
	public PeopleController(PeopleService service){
		this.service = service;
	}
	
	@GetMapping("/{id}")
	public Person getPerson(@PathVariable("id") int id){
		return peopleSerrvice.findOne(id); //may throw PeopleNotFoundException
	}
	
	@ExceptionHandler
	private ResponseEntity<PersonErrorResponse> handleException(PeopleNotFoundException e){
		PersonErrorResponse response = new PersonErrorRespponse(
		"person with this id was not found");
		
		return ResponseEntity<>(response, HttpStatus.NOT_FOUND); 
	}
}
```

Здесь:
- `PeopleErrorResponse` - класс, который мы создали для пересылки сообщения об ошибке.

- `ResponseEntity` - класс Spring Web для  описания ответа

----
#### [[@ExceptionHandler - Spring Web - Flashcards|Link to flashcards]]



---
### References:

- [[@RequestBody - Spring Web]]