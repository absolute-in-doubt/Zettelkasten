
26-01-2026 15:36

Status: #child

Tags: [[Hibernate & JPA]] [[Java+]]

---
# All query types - JPA

## JPQL (Java Persistence Query Language)

Объектно-ориентированный язык запросов, похожий на SQL, но работает с сущностями и их полями вместо таблиц.  
Нужен для типизированных, переносимых CRUD-операций и сложных выборок. Поддерживает параметры и named queries **~={purple}@NamedQuery=~**.  
**Пример:**

```java
Query query = em.createQuery("SELECT u FROM User u WHERE u.age > :age");
query.setParameter("age", 18);
List<User> users = query.getResultList();
```

## Native SQL Queries

Выполнение чистого SQL-кода для специфических возможностей БД (процедуры, сложные агрегаты).  
Нужны, когда JPQL недостаточно выразителен, но теряется переносимость между СУБД.  
**Пример:**

```java
Query query = em.createNativeQuery("SELECT * FROM users WHERE age > ?", User.class);
query.setParameter(1, 18);
List<User> users = query.getResultList();
```

## Criteria API

Программная типобезопасная сборка запросов через fluent API (CriteriaBuilder).  
Нужна для динамических запросов с условиями на основе runtime-данных (фильтры UI, поиск).  

**Пример:**

```java
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<User> cq = cb.createQuery(User.class);
Root<User> root = cq.from(User.class);
cq.select(root).where(cb.gt(root.get("age"), 18));
List<User> users = em.createQuery(cq).getResultList();
```

---
### NamedQuery

**Назначение**: Предварительно объявленные JPQL-запросы с именем для переиспользования в коде. Компилируются при загрузке persistence unit, что ускоряет выполнение и упрощает рефакторинг.

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


## TypedQuery

**Назначение**: Типизированная обёртка над обычным `Query`. Указывает тип возвращаемого результата на этапе компиляции, исключая `ClassCastException`.

**Пример**:

```java
// TypedQuery - типобезопасно
TypedQuery<User> tQuery = em.createQuery(
    "SELECT u FROM User u WHERE u.id = :id", 
    User.class
);
tQuery.setParameter("id", 1L);
User user = tQuery.getSingleResult(); // User, не Object!

// Обычный Query - требует cast
Query query = em.createQuery("SELECT u FROM User u WHERE u.id = :id");
query.setParameter("id", 1L);
User user = (User) query.getSingleResult(); // Риск ClassCastException
```

## NamedQuery + TypedQuery

Комбинированный подход — лучший выбор:

```java
TypedQuery<User> namedTypedQuery = em.createNamedQuery("User.findByAge", User.class);
namedTypedQuery.setParameter("age", 18);
return namedTypedQuery.getSingleResult();
```

----
#### [[All query types - JPA - Flashcards|Link to flashcards]]



---
### References:

