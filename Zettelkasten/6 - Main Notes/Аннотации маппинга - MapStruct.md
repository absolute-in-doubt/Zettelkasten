
23-05-2026 16:58

Status:

Tags: [[MapStruct]] [[Java+]]

---
# Аннотации маппинга - MapStruct



### @Mapping

У неё множество use case-ов:

- default value

```java
@Mapping(target = "password", defaultValue = "pass123")
User fromUserCreateDto(UserCreateDto userCreateDto);
```

_Здесь мы даем инструкцию MapStruct, что хотим установить в поле_ **_password_** _нашего_ **_target_** _объекта User_ **_значение по умолчанию_** "pass123"

> [!warning] 
> `defaultValue` устанавливает значение, **если** `source` == **null**, а если нужно вне зависимости от условий - это `constant`. 


- constant

```java
@Mapping(target = "allowed", constant = "Boolean.FALSE")
```



- ignore

```java
@Mapping(target = "email", ignore = true)
UserResponse toUserResponse(User user);
```

С помощью **"ignore"** мы говорим **MapStruct**: **игнорируй** поле **email** при маппинге. В таком случае значение **user.email** будет проигнорировано, и в **UserResponse** не будет установлено это значение _(это может использоваться для того, чтобы MapStruct не ругался на то, что таких полей нету у_ **_source_**_-объекта)_


> _Однако в случае, когда мы имеем_ **_множество полей_**_, которые необходимо проигнорировать при маппинге, прописывать_ **_@Mapping(ignore = true)_** _слишком долго._

И для такой проблемы у MapStruct **есть решение**: дополнительное свойство **unmappedTargetPolicy** у @Mapper**,** которое указывает, как должен реагировать MapStruct на те поля, которых он не нашел в объекте source:

- **_ERROR_** _- пробрасывание ошибки в случае, когда нужное поле отсутствует_
    
- **_WARN_** _(default) - неприятное красное сообщение, которое пробрасывается по умолчанию, когда вы собираете свой проект_
    
- **_IGNORE_** _- игнорировать все поля, которые не удалось смаппить (такой же эффект, как от "ignore = true")_


```java
@Mapper(componentModel = MappingConstants.ComponentModel.SPRING,        unmappedTargetPolicy = ReportingPolicy.IGNORE)
public interface UserMapper {  
  
    @Mapping(target = "updatedAt", expression = "java(java.time.LocalDateTime.now())")  
    @Mapping(target = "createdAt", expression = "java(java.time.LocalDateTime.now())")  
    @Mapping(target = "active", constant = "Boolean.TRUE")  
    User toEntity(CreateUserDto createUserDto);  
  
    UserResponseDto toDto(User user);  
  
    @Mapping(target = "updatedAt", expression = "java(java.time.LocalDateTime.now())")  
    void updateEntity(UpdateUserDto updateUserDto, @MappingTarget User user);  
}
```



- expression

**Expression** - способ задавать любой Java код. Мы также можем вызывать методы, как внутри UserMapper, так и методы библиотеки.

```java
@Mapping(target = "updatedAt", expression = "java(java.time.LocalDateTime.now())") 
    void updateEntity(UpdateUserDto updateUserDto, @MappingTarget User user);  
```

----
#### [[Аннотации маппинга - MapStruct - Flashcards|Link to flashcards]]



---
### References:

