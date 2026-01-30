
26-01-2026 20:34

Status:

Tags: [[Spring]]

---
# @Lock - optimistic & pessimistic Spring-orm


Ставится на методах репозитория.

```java
interface MyEntityRepository extends JpaRepository<Entity, Integer>{
	
	@Lock(LockModeType.PESSIMISTIC_WRITE)
	public void find(Integer id);	
} 
```

#### Установка таймаута блокировки

1. Через  `application.properties`:
```
# 10 сек глобально
spring.jpa.properties.jakarta.persistence.lock.timeout=10000  
```

2. В самой аннотации
```java
@Lock(value = LockModeType.PESSIMISTIC_WRITE, 
      arguments = {@NamedAttributeNode(name = "jakarta.persistence.lock.timeout", value = "5000")})
```


### Тип блокировки

Использует стандартные типы блокировок JPA - **[[LockModeType & PessimisticLockScope - JPA|LockModeType]]**

И для optimistic blocking дополнительно **[[LockModeType & PessimisticLockScope - JPA#Расширение области блокировки (Hibernate)|LockScope]]**

----
#### [[@Lock - optimistic & pessimistic Spring-orm - Flashcards|Link to flashcards]]



---
### References:

- [[Pessimistic blocking - Hibernate]]
- [[Optimistic  blocking - Hibernate]]
- [[LockModeType & PessimisticLockScope - JPA]]
- 