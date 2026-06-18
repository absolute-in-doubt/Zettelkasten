
02-03-2026 11:00

Status:

Tags:

---
# @ControllerAdvice & @RestControllerAdvice - Spring Web MVC


**Purpose:** Global exception handling and data binding across all controllers.

## Key Features

1. **Global exception handling** - catch exceptions from any controller
    
2. **Model attributes** - add data to all controller responses
    
3. **Data binding** - customize request parameter binding globally
    

## Basic Usage

### 1. Global Exception Handler

```java
@ControllerAdvice
public class GlobalExceptionHandler {
    
    @ExceptionHandler(NotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(NotFoundException e) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
            .body(new ErrorResponse(e.getMessage()));
    }
    
    @ExceptionHandler(DataIntegrityViolationException.class)
    public ResponseEntity<ErrorResponse> handleDataIntegrity(DataIntegrityViolationException e) {
        return ResponseEntity.status(HttpStatus.CONFLICT)
            .body(new ErrorResponse("Data integrity violation"));
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneric(Exception e) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body(new ErrorResponse("Internal server error"));
    }
}
```


---
### 2. @RestControllerAdvice (For REST APIs)

**~={purple}@RestControllerAdvice=~**  = *~={purple}**@ControllerAdvice**=~* + **~={purple}@ResponseBody=~**

То есть, не нужно использовать `ResponseEntity<>` - возвращаемое значение и так будет распаршено и передано в теле ответа

```java
@RestControllerAdvice  // Combines @ControllerAdvice + @ResponseBody
public class RestExceptionHandler {
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public ErrorResponse handleValidation(MethodArgumentNotValidException e) {
        List<String> errors = e.getBindingResult()
            .getFieldErrors()
            .stream()
            .map(FieldError::getDefaultMessage)
            .toList();
        return new ErrorResponse(errors);
    }
}
```


---
### 3. Scoped to Specific Controllers

```java
@ControllerAdvice(assignableTypes = {WorkoutsController.class, AuthController.class})
public class ApiExceptionHandler {
    // Only handles exceptions from specified controllers
}

@ControllerAdvice(basePackages = "com.example.fitness.tracker.controller")
public class ControllerPackageAdvice {
    // Only handles exceptions from specified package
}
```


---
### 4. Add Global Model Attributes

```java
@ControllerAdvice
public class GlobalControllerAdvice {
    
    @ModelAttribute("appVersion")
    public String addAppVersion() {
        return "1.0.0";  // Available in all controller responses
    }
}
```


---
### 5. Custom Data Binding

```java
@ControllerAdvice
public class DataBindingAdvice {
    
    @InitBinder
    public void initBinder(WebDataBinder binder) {
        binder.registerCustomEditor(Date.class, new CustomDateEditor());
    }
}
```


---
## Real-World Example for Your Project

```java
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {
    
    @ExceptionHandler(NotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ErrorResponseDto handleNotFound(NotFoundException e) {
        log.warn("Resource not found: {}", e.getMessage());
        return new ErrorResponseDto(e.getMessage());
    }
    
    @ExceptionHandler(UsernameIsAlreadyTakenException.class)
    @ResponseStatus(HttpStatus.CONFLICT)
    public ErrorResponseDto handleUsernameTaken(UsernameIsAlreadyTakenException e) {
        return new ErrorResponseDto(e.getMessage());
    }
    
    @ExceptionHandler(AuthenticationException.class)
    @ResponseStatus(HttpStatus.FORBIDDEN)
    public ErrorResponseDto handleAuthentication(AuthenticationException e) {
        log.warn("Authentication failed: {}", e.getMessage());
        return new ErrorResponseDto("Invalid credentials");
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public ErrorResponseDto handleValidation(MethodArgumentNotValidException e) {
        List<String> errors = e.getBindingResult()
            .getFieldErrors()
            .stream()
            .map(FieldError::getDefaultMessage)
            .toList();
        return new ErrorResponseDto(errors);
    }
    
    @ExceptionHandler(DataIntegrityViolationException.class)
    @ResponseStatus(HttpStatus.CONFLICT)
    public ErrorResponseDto handleDataIntegrity(DataIntegrityViolationException e) {
        log.error("Data integrity violation", e);
        return new ErrorResponseDto("Data integrity violation");
    }
    
    @ExceptionHandler(Exception.class)
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    public ErrorResponseDto handleGeneric(Exception e) {
        log.error("Unexpected error", e);
        return new ErrorResponseDto("Internal server error");
    }
}
```


---
### Key Differences

| Annotation                            | Use Case                        |
| ------------------------------------- | ------------------------------- |
| **~={purple}@ControllerAdvice=~**     | MVC controllers returning views |
| **~={purple}@RestControllerAdvice=~** | REST APIs returning JSON/XML    |

---
## ~={cyan}Execution Order=~

1. Controller-specific  -> ~={purple}**@ExceptionHandler**=~
	
2. **~={purple}@ControllerAdvice=~** with specific scope
    
3. Global. **~={purple}@ControllerAdvice=~** 
    
4. ~={cyan}Most specific exception type wins=~
    




----
#### [[@ControllerAdvice & @RestControllerAdvice - Spring Web MVC - Flashcards|Link to flashcards]]



---
### References:

