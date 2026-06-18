
14-02-2026 16:14

Status:

Tags: [[Hibernate & JPA]]

---
# @Enumerated - JPA


Нужно для отображения полей типа Enum:

```java
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    @Enumerated(EnumType.STRING)
    private Role role;
}

public enum Role {
    ADMIN,
    USER,
    GUEST
}
```

В БД будет храниться строка `"ADMIN"`, `"USER"`, `"GUEST"`.


- `EnumType.STRING` -  храним в виде VARCHAR
- `EnumType.ORDINAL` - хранится в виде INT


### Маппинг в [[PostgreSQL - Enum types|Postgres ENUM type]]

Используйте `@JdbcType` для нативного PostgreSQL ENUM:

```java
import org.hibernate.annotations.JdbcType;
import org.hibernate.dialect.PostgreSQLEnumJdbcType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.EnumType;

@Entity
public class User {
    @Id
    private Long id;
    
    @Enumerated(EnumType.STRING)
    @JdbcType(PostgreSQLEnumJdbcType.class)
    @Column(columnDefinition = "user_role")  // имя вашего PG ENUM типа
    private UserRole role;
}

enum UserRole {
    ADMIN, USER, GUEST
}
```



### [[PostgreSQL - Enum types#Маппинг JPA сущностей к Postgres ENUM|Маппинг JPA сущностей к Postgres ENUM]]

----
#### [[@Enumerated - JPA - Flashcards|Link to flashcards]]



---
### References:

- [[PostgreSQL - Enum types]]