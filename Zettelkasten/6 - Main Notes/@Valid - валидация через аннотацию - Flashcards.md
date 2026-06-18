
Theory for the cards: [[@Valid, @Validated - валидация через аннотацию]]

FILE TAGS: java_interview

Q: Что ты знаешь про антонации @Valid и @Validated? Чем они отличаются?
A:     
> [!example]
> **@Valid** нужен именно для работы с DTO. **@Valid** заставляет проверять аннотации на полях объекта, а не только сам объект
	
На простых параметрах (Long, String, Integer ...), где не нужно валидировать поля объекта, ставим constraints прямо на самом методе. И ставить `@Valid` - **не обязательно**:
	
```java
@Validated
@RestController
class UserController {
	
    @GetMapping("/users/{id}")
    public ResponseEntity<Void> getUser(
            @PathVariable("id") @Positive @NotNull Long id
    ) { ... }
}
```
---
	
> [!warning]
> `@Validated` — это **аннотация Spring**, а не часть Jakarta Bean Validation и не Hibernate Validator.
	
Аннотация `@Validated` — это **расширение Spring**, и её основная роль на уровне класса — **включить Spring‑механизм валидации на уровне методов и параметров** (в контроллерах, сервисах, компонентах).
	
**Что делает `@Validated` на уровне класса**
	
- Включает автоматическую валидацию **параметров методов** (например, `@Min`, `@NotNull`, `@NotBlank` прямо на параметрах `@RestController` или `@Service`), если они помечены `@Valid` или групп‑аннотациями.
    
- Позволяет использовать **группы валидации** и **последовательности групп** (например, раздельная валидация для создания и обновления сущности).
    
- Не нужна, если ты проверяешь только `@RequestBody`‑объект с `@Valid` в контроллере; `@Validated` на классе потребуется, как только захочешь валидировать **простые параметры метода** (`String`, `Integer`, `Long` и т.п.) или перейти к группам.
	
---
**Использование групп валидации через @Validated**
	
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
	
Здесь вместо **@Valid** ставят **@Validated(`Group.class`)**, потому что **@Valid** не умеет указывать группу. 
Фактически **@Validated** выполняет ту же «объектную» валидацию, что и @Valid, но ещё и выбирает нужные constraints по группе.
<!--ID: 1771748780808-->

Q: Будет ли работать проверка сложных параметров (DTO), с аннотациями на полях этого объекта, если на уровне класса не установлена аннотация `@Validated`?
```java
public class UpdateEmployeeDto {
    @NotNull
    private String name;
	
    @Valid
    private Address address; // будет проверен тоже
}
	
@RestController
class EmployeeController{
	
	public ResponseEntity<Void> update(@Valid UpdateEmployeeDto dto){
		...
	}
}
```
A:   
**`@Valid` на параметре метода работает и без `@Validated` на уровне класса !!!**
	
А вот валидация простых полей работать не будет без `@Validated`:
	
```java
@RestController
@Validated // без неё проверка не выполнится
public class UserController {
    @GetMapping("/user")
    public UserDTO get(@Min(1) @RequestParam Long id) { ... }
}
```
<!--ID: 1776151907104-->

