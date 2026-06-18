
07-05-2026 20:56

Status:

Tags: [[Hibernate & JPA]] [[Java+]]

---
# Наследование JPA сущностей - JPA

Наследование сущностей в JPA позволяет создавать иерархии классов, где дочерние entity наследуют поля и поведение от родительских, с отображением в БД по заданной стратегии.

## Основные стратегии

JPA поддерживает три стратегии наследования через аннотацию `@Inheritance` на корневом классе.

- **SINGLE_TABLE** — все классы в одной таблице с дискриминатором (`@DiscriminatorColumn`). Экономит запросы, но создает много NULL-полей.
    
- **TABLE_PER_CLASS** — отдельная таблица для каждого класса (включая родителя). Полная нормализация, но сложные запросы к иерархии.
    
- **JOINED** — родительская таблица + таблицы наследников с JOIN. Баланс гибкости и производительности.
    

## Ключевые аннотации

| Аннотация                           | Где ставить       | Назначение                                      |
| ----------------------------------- | ----------------- | ----------------------------------------------- |
| `@Inheritance(strategy=...)`        | Корневой класс    | Задает стратегию для всей иерархии.             |
| `@DiscriminatorColumn(name="TYPE")` | Корневой класс    | Столбец для типа подкласса (String/Integer).    |
| `@DiscriminatorValue("ADMIN")`      | Подкласс          | Значение дискриминатора для конкретного класса. |
| `@MappedSuperclass`                 | Базовый класс     | Только поля наследуются, таблица НЕ создается.  |
| `@Entity`                           | Все entity-классы | Обязательна для персистентности.                |

## Пример (JOINED)

```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
@DiscriminatorColumn(name = "type")
public abstract class User {
    @Id @GeneratedValue Long id;
    String name;
}

@Entity
@DiscriminatorValue("EMPLOYEE")
public class Employee extends User {
    String department;
}

@Entity  
@DiscriminatorValue("MANAGER")
public class Manager extends User {
    List<Employee> team;
}
```

Родитель — `user` таблица, наследники — `employee`, `manager` с FK на `user.id`.

Т.е. создадутся таблицы: 
- `Employee` с `id REFERENCES User(id)` и колонкой `department`
- `Manager` с `id REFERENCES User(id)` и колонкой `team`


> [!note] Note
> Запросы полиморфны: `SELECT u FROM User u` вернет все подтипы.
> 
> SQL-запросы Hibernate генерирует автоматически
> ```sql
> -- Сохранение Manager
> INSERT INTO user (name, type) VALUES ('Иван', 'MANAGER')
>INSERT INTO manager (id, team) VALUES (1, 'team_id')
>
> -- Полиморфный запрос
> SELECT u.*, m.team FROM user u 
> LEFT JOIN manager m ON u.id = m.id 
> WHERE u.type = 'MANAGER'
> ```


---
### Использование `@MappedSuperclass`

```java
import javax.persistence.*;

// Базовый класс: общие поля, НЕ entity, НЕ сохраняется отдельно
@MappedSuperclass
public abstract class BaseEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    protected Long id;
    
    protected String name;
    
    // Геттеры/сеттеры
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}

// Entity: наследует поля, своя таблица с id + name + свои поля
@Entity
@Table(name = "employees")
public class Employee extends BaseEntity {
    private int monthsInCompany;
    
    public int getMonthsInCompany() { return monthsInCompany; }
    public void setMonthsInCompany(int months) { this.monthsInCompany = months; }
}

@Entity
@Table(name = "external_customers")
public class ExternalCustomer extends BaseEntity {
    private Long sum;
    
    public Long getSum() { return sum; }
    public void setSum(Long sum) { this.sum = sum; }
}
```


Таблицы: `employees(id, name, months_in_company)` и `external_customers(id, name, sum)` — независимые, без FK.

----
#### [[Наследование JPA сущностей - JPA - Flashcards|Link to flashcards]]



---
### References:

