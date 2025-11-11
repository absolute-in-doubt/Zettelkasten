
07-11-2025 11:01

Status: #baby

Tags: [[Jakarta Validation]] [[Java+]]

---
# Ручная валидация - Jakarta Validation

**Через** `Validator` **API (Jakarta Validation)**

- Основной интерфейс: `Validator`.
    
- Методы:
    
    - `Set<ConstraintViolation<T>>` ~={green}.validate(`object`) =~— проверка всего объекта.
    
    - `Set<ConstraintViolation<T>>` ~={green}.validateProperty(`object, "fieldName"`)=~ — проверка конкретного поля.
       
    - `Set<ConstraintViolation<T>>` ~={green}.validateValue(`Class<T>, "fieldName", value`)=~ — проверка значения без создания объекта.


Все ошибки при валидации складываются в Set.


### Пример:

```java
ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
Validator validator = factory.getValidator();

Set<ConstraintViolation<Employee>> violations = validator.validate(employee);
if (!violations.isEmpty())  
    throw new ConstraintViolationException(violations);
```


----
#### [[Ручная валидация - Jakarta Validation - Flashcards|Link to flashcards]]



---
### References:

- [[@Valid, @Validated - валидация через аннотацию]]
