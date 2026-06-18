
25-05-2026 14:33

Status:

Tags: [[Spring Data JPA]] [[Spring]]

---
# Query + native SQL - Spring Data


### Что такое Native SQL в @Query?

**`@Query` с `nativeQuery = true`** позволяет выполнять **сырые SQL-запросы** (не JPQL) напрямую к базе данных. Это полезно, когда: 

- Нужно использовать **специфичные для БД функции** (например, `POSTGRESQL`/`ORACLE`). 
- Нужно **оптимизировать сложные запросы**, которые трудно выразить в JPQL. 
- Работаете с **нестандартными таблицами или представлениями**.

---

## **Базовый синтаксис**

- **`value`** — сам SQL-запрос.
- **`nativeQuery = true`** — указывает, что это **нативный SQL** (а не JPQL).
- **`@Param`** — привязка параметров (аналогично JPQL).

---

## **Примеры использования**

---

### **🔹 Пример 1: Простой SELECT**

**Задача:** Найти всех пользователей с определённым именем.

```java
@Query(
    value = "SELECT * FROM users WHERE name = :name",
    nativeQuery = true
)
List<User> findUsersByNameNative(@Param("name") String name);
```

---

### **🔹 Пример 2: UPDATE с @Modifying**

**Задача:** Обновить имя пользователя по ID.

```java
public interface UserRepository extends JpaRepository<User, Long> {

    @Query(
        value = "SELECT * FROM users WHERE name = :name",
        nativeQuery = true
    )
    List<User> findByNameNative(@Param("name") String name);
}
```

- **`@Modifying`** — обязателен для **UPDATE/DELETE/INSERT**.
- **Возвращает** количество обновлённых строк.

---

### **🔹 Пример 3: DELETE**

**Задача:** Удалить пользователей по статусу.

```java
@Modifying
@Query(
    value = "DELETE FROM users WHERE status = :status",
    nativeQuery = true
)
int deleteByStatusNative(@Param("status") String status);
```

---

### **🔹 Пример 4: Использование функций БД**

**Задача:** Найти пользователей, созданных в текущем месяце (используя `EXTRACT` в PostgreSQL).

```java
@Query(
    value = "SELECT * FROM users WHERE EXTRACT(MONTH FROM created_at) = EXTRACT(MONTH FROM CURRENT_DATE)",
    nativeQuery = true
)
List<User> findUsersCreatedThisMonthNative();
```

---

### **🔹 Пример 5: JOIN с нативным SQL**

**Задача:** Найти все заказы пользователя с именем "Ivan".

- **Внимание:** Результат должен маппиться на сущность `Order`.

```java
@Query(
    value = "SELECT o.* FROM orders o JOIN users u ON o.user_id = u.id WHERE u.name = :name",
    nativeQuery = true
)
List<Order> findOrdersByUserNameNative(@Param("name") String name);
```

---

### **🔹 Пример 6: Использование `?1`, `?2` вместо `@Param`**

```java
@Query(
    value = "SELECT * FROM users WHERE age > ?1 AND status = ?2",
    nativeQuery = true
)
List<User> findByAgeAndStatusNative(int age, String status);
```

---

## **📌 4. Маппинг результатов на сущности**

### **✅ Автоматический маппинг**

Если имена столбцов в SQL **соответствуют полям сущности**, Spring Data JPA **автоматически смаппит** результат. **Пример:**

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    private Long id;
    private String name;
    private int age;
    // Геттеры и сеттеры
}
```


```java
@Query(value = "SELECT id, name, age FROM users WHERE age > 18", nativeQuery = true)
List<User> findAdultsNative();
```

→ **Работает**, если имена столбцов (`id`, `name`, `age`) совпадают с полями сущности.

---

### **❌ Проблемы с маппингом**

Если имена столбцов **не совпадают** с полями сущности, нужно:

1. **Использовать псевдонимы** в SQL:

```java
@Query(
    value = "SELECT id as user_id, full_name as name, user_age as age FROM users WHERE age > 18",
    nativeQuery = true
)
List<User> findAdultsWithAliasesNative();
```

- **Но:** Spring Data JPA **не сможет смаппить** `user_id` на `id` автоматически.

2. **Использовать `@SqlResultSetMapping`** (для сложных случаев):

```java
@SqlResultSetMapping(
    name = "UserMapping",
    classes = @ConstructorResult(
        targetClass = User.class,
        columns = {
            @ColumnResult(name = "user_id", type = Long.class),
            @ColumnResult(name = "full_name", type = String.class),
            @ColumnResult(name = "user_age", type = Integer.class)
        }
    )
)
@Entity
@Table(name = "users")
public class User {
    // Поля и конструктор
    public User(Long id, String name, int age) { ... }
}
```

---

## **📌 5. Особенности и ограничения**

|Особенность|Описание|
|---|---|
|**Параметры**|Можно использовать `@Param("name")` или `?1`, `?2`.|
|**@Modifying**|Обязателен для **UPDATE/DELETE/INSERT**.|
|**Транзакции**|Методы с `@Modifying` должны вызываться в **транзакции** (`@Transactional`).|
|**Маппинг**|Автоматический маппинг работает, если имена столбцов совпадают с полями сущности.|
|**Производительность**|Native SQL может быть **быстрее** JPQL для сложных запросов.|
|**Портативность**|Запросы **зависят от СУБД** (PostgreSQL, MySQL, Oracle и т.д.).|
|**Безопасность**|Используйте **параметры** (`@Param` или `?1`), чтобы избежать SQL-инъекций.|

---

## **📌 6. Пример с @Transactional**

- **Почему `@Transactional`?** 
~={red}Методы с `@Modifying` **требуют активной транзакции**.=~

```java
@Service
@RequiredArgsConstructor
public class UserService {
    private final UserRepository userRepository;

    @Transactional
    public int updateUserNameNative(Long id, String newName) {
        return userRepository.updateNameByIdNative(id, newName);
    }
}
```

---

## **📌 7. Итоговая таблица**

Сводная таблица по @Query с Native SQL

|Тип запроса|Пример|Аннотации|Возвращаемый тип|
|---|---|---|---|
|**SELECT**|`SELECT * FROM users WHERE name = :name`|`@Query(nativeQuery = true)`|`List`, `User`, `Stream`|
|**UPDATE**|`UPDATE users SET name = :name WHERE id = :id`|`@Modifying`, `@Query(nativeQuery = true)`|`int` (количество обновлённых строк)|
|**DELETE**|`DELETE FROM users WHERE status = :status`|`@Modifying`, `@Query(nativeQuery = true)`|`int` (количество удалённых строк)|
|**INSERT**|`INSERT INTO users (name, age) VALUES (:name, :age)`|`@Modifying`, `@Query(nativeQuery = true)`|`int` (количество вставленных строк)|

---

## **📌 8. Выводы**

✅ **`@Query(nativeQuery = true)`** позволяет выполнять **сырые SQL-запросы**. 

✅ **Подходит для** сложных запросов, специфичных для БД функций, оптимизации. 

✅ **Для UPDATE/DELETE/INSERT** используйте **`@Modifying` + `@Transactional`**. 

✅ **Маппинг результатов** работает автоматически, если имена столбцов совпадают с полями сущности. 

❌ **Не портативно** — зависит от СУБД. 

❌ **Осторожно с SQL-инъекциями** — всегда используйте параметры (`@Param` или `?1`).

----
#### [[Query + native SQL - Spring Data - Flashcards|Link to flashcards]]



---
### References:

