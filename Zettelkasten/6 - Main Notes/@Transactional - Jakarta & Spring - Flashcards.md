
Theory for the cards: [[@Transactional - Jakarta & Spring ORM]]

FILE TAGS: spring_data spring

Q: Как можно установить уровень изоляции транзакции в Spring Data?
A: **Через аннотацию @Transactional** - локально
```java
@Transactional(isolation = Isolation.REPEATABLE_READ)
```
Spring автоматически применит настройку к Connection через JpaTransactionManager. 
	
	
**Глобально:** 
в `persistence.xml` или `application.properties` через `spring.jpa.properties.hibernate.connection.isolation=4`.
<!--ID: 1769429821795-->


Q: Перечисли виды `propagation` в `@Transactional`.
A:  `propagation` в `@Transactional` определяет, как метод участвует в транзакции:
	
- `REQUIRED` — использовать текущую транзакцию или создать новую.
    
- `REQUIRES_NEW` — всегда создавать новую, текущую приостанавливается.
    
- `MANDATORY` — метод должен вызываться внутри существующей транзакции.
    
- `SUPPORTS` — использовать текущую транзакцию, если она есть.
    
- `NOT_SUPPORTED` — выполнять без транзакции.
    
- `NEVER` — запрет на выполнение внутри транзакции.
    
- `NESTED` — вложенная транзакция через savepoint внутри текущей.
<!--ID: 1782055885479-->

Q: Какие ты знаешь свойства `@Transactional` (параметры)?
A:      
![[Pasted image 20260621183255.png]]
<!--ID: 1782055982086-->
