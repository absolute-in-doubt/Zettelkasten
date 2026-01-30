
Theory for the cards: [[@ExceptionHandler - Spring Web MVC]]

FILE TAGS: spring

Q: Для чего нужен @ExceptionHandler в Spring Web MVC?
A: Он нужен для согласованной обработки ошибок (логика в одном месте).
	
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
<!--ID: 1769525050069-->
