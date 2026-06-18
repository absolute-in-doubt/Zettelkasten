
14-02-2026 16:10

Status:

Tags: [[PostgreSQL DB]]

---
# PostgreSQL - Enum types


![[Pasted image 20260214161221.png]]

`+` компактное хранение.

Из альтернатив:
- хранение Enum-ов в строке - не эффективно
- хранение в int-ах + референсная табличка для аналитиков (никак не участвует в коде) -> нужно поддерживать, не забывать обновлять, писать миграции



```sql
CREATE TYPE user_role AS ENUM ('ADMIN', 'USER', 'GUEST');
```

Использование ENUM для поля в таблице:

```sql
CREATE TABLE customer_order (
    id BIGINT PRIMARY KEY,
    status order_status
);
```

### Удаление

```sql
DROP TYPE pricebartimeframe;
```


### Просмотр значений

```sql
SELECT t.typname, e.enumlabel
FROM pg_type t
JOIN pg_enum e ON t.oid = e.enumtypid
WHERE t.typname = 'pricebartimeframe';
```


---
### Маппинг JPA сущностей к Postgres ENUM

```java
@Entity
public class CustomerOrder {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
	
    @Enumerated(EnumType.STRING)
    @JdbcType(PostgreSQLEnumJdbcType.class)
    private OrderStatus status;  // Java enum: PENDING, IN_PROGRESS и т.д.
}
```

> [!warning]
> Не отдельно `@JdbcType(PostgreSQLEnumJdbcType.class)`, а вместе с `@Enumerated(EnumType.STRING)`.


----
#### [[PostgreSQL - Enum types - Flashcards|Link to flashcards]]



---
### References:

- [[@Enumerated - JPA]]