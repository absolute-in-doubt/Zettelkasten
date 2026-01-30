
26-01-2026 20:48

Status: #adult

Tags: [[Hibernate & JPA]] [[Java+]]

---
# LockModeType & PessimisticLockScope - JPA

### Использует `LockModeType`, определённый в JPA:

**Pessimistic**

1. `LockModeType.PESSIMISTIC_READ`- блокирует любое изменение записи до тех пор пока транзакция не завершит свою работу, но при этом разрешает другим сущностям её читать. Позволяет не допустить [[ACID principles - БД#Аномалии чтения данных|dirty read]]. Это называется общей блокировкой, так как блокируется запись одной транзакцией, но доступ для чтения разрешен для всех.
	
	- Реализуется через [[Pessimistic blocking - SQL#`SELECT ... FOR SHARE` / `FOR SHARE MODE`|SELECT ... FOR SHARE]]
    
2. `LockModeType.PESSIMISITIC_WRITE`- блокирует любое чтение и изменение записи до тех пор пока транзакция не завершит свою работу. Это называется эксклюзивная блокировка, так как предоставляет запись только для одной транзакции.
	
	- Реализуется через [[Pessimistic blocking - SQL#`SELECT ... FOR UPDATE`|SELECT ... FOR UPDATE]]
    
3. `LockModeType.PESSIMISTIC_FORCE_INREMENT`- совмещение пессимистической`write`блокировки и оптимистической. Позволяет предотвратить ситуации, когда сначала первая транзакция с помощью оптимистической блокировки считала сущность, затем мы пессимистично захватили эту же запись во второй транзакции, завершились быстрее чем первая транзакция, то первая транзакция перетрет наши данные ([[ACID principles - БД#Аномалии чтения данных|lost update]]). Этот вид блокировки не даст этому случиться.  
	
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
## Расширение области блокировки (Hibernate)

Hibernate поддерживает `PessimisticLockScope` для блокировки связанных сущностей:


Виды областей блокировки:

| Scope                   | Описание                                                         | Что блокирует БД                         |
| ----------------------- | ---------------------------------------------------------------- | ---------------------------------------- |
| `NORMAL` (по умолчанию) | Только основную таблицу сущности + secondary tables              | `SELECT ... FOR UPDATE` на `Entity`      |
| `EXTENDED`              | Сущность + строки коллекций (`@OneToMany`, `@ElementCollection`) | `FOR UPDATE` на parent + collection rows |

#### Настройка

**Hibernate:**

- Локально на вызове:
```java
Map<String, Object> props = Map.of(
    "jakarta.persistence.lock.scope", PessimisticLockScope.EXTENDED
);
entityManager.find(
    Post.class, 1L, 
    LockModeType.PESSIMISTIC_WRITE, 
    props
);
```

- В Query
```java
Query query = em.createQuery("SELECT p FROM Post p WHERE p.id = :id");
query.setLockMode(LockModeType.PESSIMISTIC_WRITE);
query.setHint("jakarta.persistence.lock.scope", PessimisticLockScope.EXTENDED);
```


**Spring Data**

- в `application.properties`
```
# NORMAL (по умолчанию) или EXTENDED
spring.jpa.properties.jakarta.persistence.lock.scope=EXTENDED
```

- Через @QueryHints
```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
@QueryHints(value = {
    @QueryHint(name = "jakarta.persistence.lock.scope", value = "EXTENDED")
})
@Query("SELECT p FROM Post p JOIN FETCH p.comments WHERE p.id = :id")
Post findPostWithComments(@Param("id") Long id);
```


----
#### [[LockModeType & PessimisticLockScope - JPA - Flashcards|Link to flashcards]]



---
### References:

- [[@Lock - optimistic & pessimistic Spring-orm]]
