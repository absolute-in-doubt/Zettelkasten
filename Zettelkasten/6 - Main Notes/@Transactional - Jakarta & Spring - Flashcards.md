
Theory for the cards: 

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

