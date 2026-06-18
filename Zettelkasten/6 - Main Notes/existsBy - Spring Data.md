
30-05-2026 13:17

Status:

Tags: [[Spring Data JPA]] [[Spring]]

---
# existsBy - Spring Data


### `existsBy(Example<T> probe)` в Spring Data JPA

Позволяет проверить существование сущности по шаблону (**Query by Example, QBE**).

**Сигнатура:**

```java
boolean exists(Example<T> example)
```

**Как работает:**

1. Создается объект-сущность (`probe`) с заполненными полями для поиска.
    
2. Из него формируется `Example`.
    
3. Spring Data строит запрос по непустым полям `probe`.
    
4. Возвращает `true`, если найден хотя бы один результат.


**Особенности:**

- ~={red}**`null`-поля игнорируются.**=~
    
- Поддерживает настройку через `ExampleMatcher`.
    
- Не требует написания JPQL или методов вида `existsByEmailAndStatus`.
    
- Удобен для динамических критериев поиска.
    

**Ограничения:**

- Работает только с простыми условиями (`AND` между полями).
    
- Не поддерживает сложные запросы, группировки, подзапросы.
    
- Для сложной фильтрации лучше использовать `Specification` или QueryDSL.
    

**Типичный сценарий:**  
Проверить существование записи по набору динамически заданных полей без создания отдельного метода репозитория.

---
### Spring Data custom queries тоже понимают такой синтаксис

```java
@Repository  
public interface UserCredentialsRepository extends JpaRepository<UserCredentials, Long> {  
      
    boolean existsByLogin(String login);  
}
```


Будет преобразован в что-то типа:

```sql
select count(*) > 0
from user_credentials
where login = ?
```


----
#### [[existsBy - Spring Data - Flashcards|Link to flashcards]]



---
### References:

