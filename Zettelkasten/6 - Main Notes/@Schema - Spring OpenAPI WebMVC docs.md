
21-02-2026 11:20

Status:

Tags: [[Spring Docs (OpenAPI & Swagger)]]

---
# @Schema - Spring OpenAPI WebMVC docs


Для описания DTO классов и их полей.

```java
@ToString  
@Getter  
@Setter  
@Schema(description="Registration request")  
public class RegisterRequestDto {  
    @Schema(description="First name. Size =< 25.")  
    @NotBlank(message="first name shouldn't be empty")  
    @Size(max=25, message="first name size shouldn't exceed 25 characters")  
    @JsonProperty(value="first_name")  
    private String firstName;  
    @Schema(description = "Last name. Size =< 25.")  
    @NotBlank(message="last name shouldn't be empty")  
    @Size(max=25, message="last name size shouldn't exceed 25 characters")  
	
	...
	
    @Schema(description = "User's roles ('ROLE_USER', 'ROLE_ADMIN').")  
    @NotNull(message="roles shouldn't be empty")  
    private List<Role> roles;  
}
```


----
#### [[@Schema - Spring OpenAPI WebMVC docs - Flashcards|Link to flashcards]]



---
### References:

