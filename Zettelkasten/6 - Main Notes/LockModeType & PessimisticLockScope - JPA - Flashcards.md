
Theory for the cards:  [[LockModeType & PessimisticLockScope - JPA]]

FILE TAGS: jpa

Q: Какие есть типы opimistic/pessimistic блокировок в JPA? Где и как их настраивать?
A:    
**Pessimistic**
	
1. `LockModeType.PESSIMISTIC_READ`- блокирует любое изменение записи до тех пор пока транзакция не завершит свою работу, но при этом разрешает другим сущностям её читать. Позволяет не допустить **dirty read**. Это называется общей блокировкой, так как блокируется запись одной транзакцией, но доступ для чтения разрешен для всех.
	
	- Реализуется через `SELECT ... FOR SHARE`
    
2. `LockModeType.PESSIMISITIC_WRITE`- блокирует любое чтение и изменение записи до тех пор пока транзакция не завершит свою работу. Это называется эксклюзивная блокировка, так как предоставляет запись только для одной транзакции.
	
	- Реализуется через `SELECT ... FOR UPDATE`
    
3. `LockModeType.PESSIMISTIC_FORCE_INREMENT`- совмещение пессимистической`write`блокировки и оптимистической. Позволяет предотвратить ситуации, когда сначала первая транзакция с помощью оптимистической блокировки считала сущность, затем мы пессимистично захватили эту же запись во второй транзакции, завершились быстрее чем первая транзакция, то первая транзакция перетрет наши данные (**lost update**)). Этот вид блокировки не даст этому случиться.  
	
   Тут мы при старте транзакции с pessimistic lock сразу же увеличим версию (незваисимо, изили ли entity) для предотвращения этого. (пусть это и pessimistic block)
	
	
**Optimistic**
	
1. `LockModeType.OPTIMISTIC`- блокировка по умолчанию для всех сущностей, помеченных аннотацией`@Version`.
	
2. `LockModeType.OPTIMISTIC_FORCE_INCREMENT`- блокировка полностью аналогичная прошлой. Но **всегда** увеличивает `@Version`, независимо от изменений полей.
	
```java
@Entity 
class Post { 
    @Version int version; 
    @OneToMany(mappedBy="post") List<Comment> comments; 
}
	
@Entity 
class Comment { 
    Post post;  // @ManyToOne
}
	
// ТОЛЬКО Comment.version++
em.lock(comment, LockModeType.PESSIMISTIC_FORCE_INCREMENT);  
// Post.version НЕ меняется автоматически!
```
	
> [!warning]
> `PESSIMISTIC_FORCE_INCREMENT` и `OPTIMISTIC_FORCE_INCREMENT` **~={cyan}не увеличивают=~ автоматически версии ~={cyan}связанных=~ таблиц** — они инкрементят **только версию той сущности, на которой вызван `lock()`**. Распространение на связанные сущности (родителей) происходит через **каскадное обновление** или `LockScope.EXTENDED`, но это отдельная настройка Hibernate.
>
> `+` Ни `OPTIMISTIC`, ни `OPTIMISTIC_FORCE_INCREMENT` **не проверяют версии связанных таблиц автоматически.** Они работают **только с версией той сущности**, на которой был вызван `lock()` или `find()` с указанным режимом.
	
---
**Где настраивать?**
	
Настраиваются на методах `EntityManager`:
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
<!--ID: 1769451574291-->


Q: Что такое LockScope в JPA? Для чего он нужен?
A: LockScope определяет поведение валидации для optimistic blocking. Есть два вида LockScope:
- `NORMAL` - проверяем совпадение версии только самой сущности. 
	`SELECT ... FOR UPDATE` на `Entity`
- `EXTENDED` - проверяем версию самой сущности + связанных с ней записей. 
	Сущность + строки коллекций (`@OneToMany`, `@ElementCollection`)
	`SELECT ... FOR UPDATE` на parent + collection rows
	
Может устанавливаться глобально (в Spring) - в `application.properties`. Но обычно ставится на методе `EntityManager`.
<!--ID: 1769451574303-->
