
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

**~={red}`@Valid` на параметре метода работает и без `@Validated` на уровне класса !!!=~**

А вот валидация простых полей работать не будет без `@Validated`:

```java
@RestController
@Validated // без неё проверка не выполнится
public class UserController {
    @GetMapping("/user")
    public UserDTO get(@Min(1) @RequestParam Long id) { ... }
}
```

---

### Что делает `@Valid`

- **~={cyan}Каскадная валидация=~**: если у сущности есть вложенные объекты, то при проверке родителя автоматически валидируются и они.

- **Применение в контроллерах**: в Spring Boot можно поставить `@Valid` на аргумент метода контроллера, и фреймворк сам вызовет валидацию.

- **Применение в сущностях**: ~={cyan}если поле класса помечено `@Valid`, то при валидации объекта будут проверены и вложенные поля.=~
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

---
### `@Validated`

	
> [!warning]
> `@Validated` — это **аннотация Spring**, а не часть Jakarta Bean Validation и не Hibernate Validator.
	
Аннотация `@Validated` — это **расширение Spring**, и её основная роль на уровне класса — **включить Spring‑механизм валидации на уровне методов и параметров** (в контроллерах, сервисах, компонентах).
	
**Что делает `@Validated` на уровне класса**
	
- Включает автоматическую валидацию **параметров методов** (например, `@Min`, `@NotNull`, `@NotBlank` прямо на параметрах `@RestController` или `@Service`), если они помечены `@Valid` или групп‑аннотациями.
    
- Позволяет использовать **группы валидации** и **последовательности групп** (например, раздельная валидация для создания и обновления сущности).
    
- Не нужна, если ты проверяешь только `@RequestBody`‑объект с `@Valid` в контроллере; `@Validated` на классе потребуется, как только захочешь валидировать **простые параметры метода** (`String`, `Integer`, `Long` и т.п.) или перейти к группам.
	


### Использование групп валидации через ~={purple}@Validated=~

На **параметре метода** `@Validated` используется, когда нужны _validation groups_:

```java
@PostMapping("/users")
public void createUser(
        @RequestBody @Validated(OnCreate.class) UserDto dto
) { ... }

@PutMapping("/users/{id}")
public void updateUser(
        @RequestBody @Validated(OnUpdate.class) UserDto dto
) { ... }
```

Здесь вместо **~={purple}@Valid=~** ставят **~={purple}@Validated(`Group.class`)=~**, потому что **~={purple}@Valid=~** не умеет указывать группу. 
Фактически **~={purple}@Validated=~** выполняет ту же «объектную» валидацию, что и ~={purple}@Valid=~, но ещё и ~={cyan}выбирает нужные constraints по группе=~.


----
#### [[@Valid - валидация через аннотацию - Flashcards|Link to flashcards]]



---
### References:

