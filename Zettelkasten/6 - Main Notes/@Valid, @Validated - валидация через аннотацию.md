
07-11-2025 10:58

Status: #baby 

Tags: [[Jakarta Validation]] [[Java+]]

---
# @Valid - валидация через аннотацию

`@Valid` находится в пакете `jakarta.validation`.

~={red}**Этот пакет интегрирован в Spring MVC**=~

Её можно ставить на 
- **поля сущности**, 
- **аргументы методов контроллеров**, 
- **аргументы сервисных методов** 
и даже на элементы коллекций:
```java
private List<@Valid Employee> employees;
```

НО:

```java
    @Service
    @Validated // важно!
    public class EmployeeService {
        public void save(@Valid Employee employee, BindingResult bindingResult) { ... }
    }
```

- Но~={cyan} без аннотации `@Validated` на классе Spring не будет запускать валидацию при вызове метода.=~

- ~={cyan}Если сервис не управляется Spring, то `@Valid` сам по себе ничего не сделает — нужно вручную вызвать `Validator`.=~

---

### Что делает `@Valid`

- **Каскадная валидация**: если у сущности есть вложенные объекты, то при проверке родителя автоматически валидируются и они.

- **Применение в контроллерах**: в Spring Boot можно поставить `@Valid` на аргумент метода контроллера, и фреймворк сам вызовет валидацию.

- **Применение в сущностях**: если поле класса помечено `@Valid`, то при валидации объекта будут проверены и вложенные поля.
```java
public class Employee {
    @NotNull
    private String name;

    @Valid
    private Address address; // будет проверен тоже
}

public class Address {
    @NotBlank
    private String city;
}
```





---
### Пример:

```java
public class Person {  
    private int id;  
  
    @NotEmpty(message="name shouldn't be empty")  
    @Size(min=2, max=30, message="Name should be between 2 and 30 characters")  
    private String name;  
  
    @Min(value=0, message="Age can't be negative")  
    private int age;  
  
    @NotEmpty(message="email shouldn't be empty")  
	@Email(message="email must be valid")
    private String email;
    ...
```

**Триггер (активация) проверки при получении объекта из формы:**
(аннотация **@Valid** на получаемом аргументе в handler методе контроллера)
```java
@PostMapping()  
public String create(@ModelAttribute("person") @Valid Person person, BindingResult bindingResult) {
  
    if(bindingResult.hasErrors())  
        return "people/new"; //если есть ошибки - сразу возвращаем пользователя обратно на формуpersonDao.save(person);
          
    personDao.save(person);  
    return "redirect:/people";  
}
```



| Аннотация    | Пакет                                          | Где применяется                                                               | Что делает                                                                                                         | Когда использовать                                                                                         |
| ------------ | ---------------------------------------------- | ----------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------- |
| `@Valid`     | `jakarta. validation`                          | На аргументах методов контроллеров, на полях сущностей, на вложенных объектах | Запускает стандартную Bean Validation, включая каскадную проверку вложенных объектов                               | В контроллерах и DTO, когда нужна базовая валидация без разделения на группы                               |
| `@Validated` | `org. springframework. validation. annotation` | На классах сервисов, на аргументах методов (вместо `@Valid`)                  | Расширяет `@Valid`: поддерживает **валидационные группы**, активирует проверку аргументов методов через Spring AOP | Когда нужно разделять правила по сценариям (например, Create vs Update), или включить валидацию в сервисах |




----
#### [[@Valid - валидация через аннотацию - Flashcards|Link to flashcards]]



---
### References:

