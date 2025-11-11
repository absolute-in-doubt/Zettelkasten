
02-11-2025 12:39

Status: #baby 

Tags: [[Spring]] 

---
# spring-orm package

Это часть **Spring Framework (spring-context, spring-jdbc, spring-tx, spring-orm)**.

Предоставляет **интеграцию с ORM**: Hibernate, JPA, JDO, MyBatis.


#### Основные возможности:
   
- Управление `EntityManager`/`SessionFactory` через Spring IoC.

- [[Поддержка декларативных транзакций - Spring-orm|Поддержка декларативных транзакций]] (`@Transactional`).

- Унифицированная иерархия исключений (перевод Hibernate/JPA ошибок в `DataAccessException`).

- Шаблонные классы (`HibernateTemplate`, `JpaTemplate`) — сейчас реже используются, но исторически упрощали работу.

----
#### [[spring-orm package - Flashcards|Link to flashcards]]



---
### References:

- [[Hibernate & JPA]]
- [[Поддержка декларативных транзакций - Spring-orm]]