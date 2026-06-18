
11-01-2026 15:59

Status:

Tags: [[Hibernate & JPA]] 

---
# JPQL запросы - JPA


## Создание запросов

Основные способы создать запрос из `EntityManager`:

- Ад‑hoc запрос строкой:
	
```java
TypedQuery<User> q = em.createQuery(
    "select u from User u where u.active = true",
    User.class
);
```
    
Здесь используется `createQuery(String jpql, Class<T>)` для типобезопасного результата.​



- Именованный запрос (`@NamedQuery`):
    
```java
@NamedQuery(
    name = "User.findActive",
    query = "select u from User u where u.active = true"
)
class User { ... }

TypedQuery<User> q = em.createNamedQuery("User.findActive", User.class);
```
	
Именованные запросы объявляются на сущности и переиспользуются по имени.


---

## Отправка запросов

После создания запроса:

- Для выборки:
    
```java
List<User> users = q.getResultList();      // несколько результатов
User user = q.getSingleResult();          // один результат
```
    
Параметры задаются через `setParameter("name", value)` или по позиции.​


- Для bulk `UPDATE` / `DELETE`:
    
```java
Query q = em.createQuery(
    "update User u set u.active = false where u.lastLogin < :date"
);
int updated = q.setParameter("date", someDate)
               .executeUpdate();
```
    
Такие запросы возвращают количество изменённых строк через `executeUpdate()` и не типизированы (`Query`, не `TypedQuery`).


---

## Пагинация (limit/offset)

Пагинация делается на объекте `Query`/`TypedQuery`:

```java
int page = 2;      // номер страницы с 0 или 1 — как вы решите
int size = 20;     // размер страницы

List<User> users = em.createQuery(
        "select u from User u order by u.id",
        User.class
    )
    .setFirstResult(page * size)   // offset
    .setMaxResults(size)          // limit
    .getResultList();
```

Методы `setFirstResult(int)` задают смещение, а `setMaxResults(int)` ограничивают количество возвращаемых записей, и работают как с JPQL, так и с Criteria‑ и частично native‑запросами.java.tutorialink+2​


---
### JOINs

```java
@Query("SELECT o FROM Order o LEFT JOIN FETCH o.item WHERE o.id = :orderId AND o.deleted = false")  
Optional<Order> findByIdWithDeletedFalse(@Param("orderId") @NotNull Long id);
```



---
### References:

- [[Custom repository methods - Spring Data]]