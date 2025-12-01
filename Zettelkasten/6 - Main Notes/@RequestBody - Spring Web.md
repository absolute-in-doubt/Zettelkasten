
30-11-2025 13:35

Status:

Tags: [[Spring]]

---
# @RequestBody - Spring Web

Ставится перед параметром метода контроллера для конвертации полученного json body в Java объекты с помощью Jackson:

```java
@PostMapping
public ResponseEntity<HttpStatus> create(@RequestBody @Valid Person person, BindingResult bindingResult) {
	StringBuilder errorMsg = new StringBuilder();
	if(bindingResult.hasErrors()){
		for(FieldError error : bindingResult.getFieldErrors())
			errorMsg.append(error)
			.append(" - ").append(error.getDefaultMessage())
			.append("/n");
		}
		
		throw new PersonNotCreatedException(errorMsg.toString());
	}
	...
}
	
	@ExceptionHandler
private ResponseEntity<PersonErrorResponse> handleException(PersonNotCreatedException e){
	PersonErrorResponse response = new PersonErrorRespponse(
	"person with this id was not found");
	
	return ResponseEntity<>(response, HttpStatus.NOT_FOUND); 
}
```

~={orange}Тут валидирум получаемый объект с помощью=~ [[Jakarta Validation]]

`BindingResult` - такой же как и при [[@Valid, @Validated - валидация через аннотацию|проверке значений с помощью Hibernate Validator]], заполняемых в HTML форме.

Если при валидации что-то невалидное -> кидаем ошибку и отлавливаем её в [[@ExceptionHandler - Spring Web|@ExceptionHandler]]


----
#### [[@RequestBody - Spring Web - Flashcards|Link to flashcards]]



---
### References:

- [[@ExceptionHandler - Spring Web]]