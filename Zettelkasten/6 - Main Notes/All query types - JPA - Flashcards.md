
Theory for the cards: [[All query types - JPA]]

FILE TAGS: jpa

Q: Перечисли все типф query которые есть в JPA.
A:   
- NamedQuery - предопределённые запросы, компилируются при загрузке persistence unit, что ускоряет выполнение.
- TypedQuery - добавляет проверку типов
- NativeQuery - на чистом SQL
- JPQL query
- Criteria API query - программная типобезопасная сборка запросов в runtime.
<!--ID: 1769431634339-->



Q: Расскажи про NamedQuery в JPA. Для чего он нужен? ПРиведи пример использования NamedQuery (Java code).
A: Предварительно объявленные **JPQL**-запросы с именем для переиспользования в коде. Компилируются при загрузке persistence unit, что ускоряет выполнение и упрощает рефакторинг.
	
**Пример объявления** (в сущности):
	
```java
@Entity
@NamedQuery(
    name = "User.findByAge", 
    query = "SELECT u FROM User u WHERE u.age > :age"
)
public class User { ... }
```
	
**Использование**:
	
```java
TypedQuery<User> query = em.createNamedQuery("User.findByAge", User.class);
query.setParameter("age", 18);
List<User> users = query.getResultList();
```
<!--ID: 1769431610589-->


