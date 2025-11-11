
08-11-2025 14:05

Status:

Tags: [[Spring]]

---
# Custom repository methods - Spring Data


В интерфейсе, расширяющем `JpaRepository<E, K>` просто прописываем сигнатуру метода - Spring Data сам её реализует.

### Конвенция именования кастомных методов (реализуемых Spring Data):

### [Именование Query-методов](https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html#jpa.query-methods.query-creation)

Пример:
```java
@Repository  
public interface PeopleJpaRepository extends JpaRepository<Person, Integer> {  
  
    List<Person> findByName(String name);  
}
```



----
#### [[Custom repository methods - Spring Data - Flashcards|Link to flashcards]]



---
### References:

