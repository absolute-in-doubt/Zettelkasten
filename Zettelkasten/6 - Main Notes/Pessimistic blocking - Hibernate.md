
21-01-2026 19:24

Status:

Tags: [[Hibernate & JPA]] [[Java+]]

---
# Pessimistic blocking - Hibernate

Это такой вид блокировки, который лочит запись до тех пор, пока не завершится транзакция. Пока запись удерживается, остальные пользователи ожидают, когда можно будет захватить запись. Если ожидание слишком долгое, то будет выброшено исключение`LockTimeoutException`. 

Время ожидания можно изменять вручную при создании блокировки с помощью добавления`Map`, в котором есть ключ`jakarta.persistence.lock.timeout`. Это исключение помечает транзакцию, как rollback.  


**~={orange}Аналог`synchronized`в Java, но только здесь используются средства баз данных.=~**

### Hibernate предоставляет три типа блокировки:
(дублирует [[LockModeType & PessimisticLockScope - JPA]])

1. `LockModeType.PESSIMISTIC_READ`- блокирует любое изменение записи до тех пор пока транзакция не завершит свою работу, но при этом разрешает другим сущностям её читать. Позволяет не допустить [[ACID principles - БД#Аномалии чтения данных|dirty read]]. Это называется общей блокировкой, так как блокируется запись одной транзакцией, но доступ для чтения разрешен для всех.
	
	- Реализуется через [[Pessimistic blocking - SQL#`SELECT ... FOR SHARE` / `FOR SHARE MODE`|SELECT ... FOR SHARE]]
    
2. `LockModeType.PESSIMISITIC_WRITE`- блокирует любое чтение и изменение записи до тех пор пока транзакция не завершит свою работу. Это называется эксклюзивная блокировка, так как предоставляет запись только для одной транзакции.
	
	- Реализуется через [[Pessimistic blocking - SQL#`SELECT ... FOR UPDATE`|SELECT ... FOR UPDATE]]
    
3. `LockModeType.PESSIMISTIC_FORCE_INREMENT`- совмещение пессимистической`write`блокировки и оптимистической. Позволяет предотвратить ситуации, когда сначала первая транзакция с помощью оптимистической блокировки считала сущность, затем мы пессимистично захватили эту же запись во второй транзакции, завершились быстрее чем первая транзакция, то первая транзакция перетрет наши данные ([[ACID principles - БД#Аномалии чтения данных|lost update]]). Этот вид блокировки не даст этому случиться.  


### Pessimistic lock scope

1. `PessimisticLockScope.NORMAL`- блокирует исключительно саму запись (если есть наследование, то блокируются все субтаблицы).
    
2. `PessimisticLockScope.SHARED`- блокирует саму запись и все её связи.  


```java
// В find()
MyEntity entity = em.find(MyEntity.class, id, LockModeType.PESSIMISTIC_WRITE);  // Исключающая блокировка

// Или отдельно
em.lock(entity, LockModeType.PESSIMISTIC_READ, Map.of("jakarta.persistence.lock.timeout", 5000));  // Таймаут 5 сек

// Query с блокировкой
em.createQuery("SELECT e FROM MyEntity e WHERE e.id = :id")
   .setParameter("id", id)
   .setLockMode(LockModeType.PESSIMISTIC_WRITE)
   .getSingleResult();
```
Блокировка держится до `commit()` транзакции или `unlock()`.

----
#### [[Pessimistic blocking - Hibernate - Flashcards|Link to flashcards]]



---
### References:

- [[Optimistic  blocking - Hibernate]]
- [[Pessimistic blocking - SQL]]
- [[@Lock - optimistic & pessimistic Spring-orm]]
- https://www.pvsm.ru/java/402412