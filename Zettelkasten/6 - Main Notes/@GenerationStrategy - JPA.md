
27-01-2026 10:46

Status:

Tags: [[Hibernate & JPA]] [[Java+]]

---
# @GenerationStrategy - JPA


| Стратегия  | Описание                                                | БД-поддержка                  | Пример                                                                                          |
| ---------- | ------------------------------------------------------- | ----------------------------- | ----------------------------------------------------------------------------------------------- |
| `AUTO`     | Автовыбор провайдером (часто `IDENTITY` или `SEQUENCE`) | Все                           | `@GeneratedValue(strategy = GenerationType.AUTO)`                                               |
| `IDENTITY` | Автоинкремент БД (INSERT → SELECT LAST_INSERT_ID)       | MySQL, PostgreSQL, SQL Server | `@GeneratedValue(strategy = GenerationType.IDENTITY)`                                           |
| `SEQUENCE` | БД-sequence (nextval перед INSERT)                      | Oracle, PostgreSQL, H2, DB2   | `@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "seq")` с `@SequenceGenerator` |
| `TABLE`    | Отдельная таблица с счётчиками (SELECT FOR UPDATE)      | Все                           | `@GeneratedValue(strategy = GenerationType.TABLE)`                                              |

- **IDENTITY** = колонка-автоинкремент
- **SEQUENCE** = независимый генератор чисел. (stored in the DB)

## Почему SEQUENCE предпочтительна

SEQUENCE — **самая эффективная** для высоконагруженных систем по сравнению с другими:

- **Батчинг INSERT**: Получает ID заранее (`allocationSize=50`), не блокирует после INSERT (в отличие от IDENTITY).​​
    
- **Масштабируемость**: Native БД-sequence без row-level locks (TABLE требует `FOR UPDATE` на таблице последовательностей).​
    
- **Нет gaps**: Минимальные пробелы при rollback (preallocation).​
    
- **Поддержка inheritance**: Работает с `@Inheritance(TablePerClass)` (IDENTITY нет).


---
### Пример использования `GenerationStrategy.SEQUENCE`

Если не включён `ddl-auto` - создаём SEQUENCE в БД вручную.

```sql
CREATE SEQUENCE user_sequence START 1 INCREMENT 50;
```

Его использование на entity классе:

```java
@Id
@SequenceGenerator(
	name = "user_seq", 
	sequenceName = "user_sequence", 
	allocationSize = 50
)
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "user_seq")
private Long id;
```

**`allocationSize`** — это **размер блока ID**, который Hibernate резервирует за один вызов к БД-sequence. Позволяет **батчить** INSERT'ы и **ускорять** генерацию ID.

**При `allocationSize=50`**:

1. Hibernate вызывает `nextval('user_seq')` → получает `100`
    
2. Локально генерирует ID: `100, 101, 102...149` **без обращений к БД**
    
3. Следующий блок: `nextval()` → `150`, затем `150-199`

```sql
-- Sequence создается с INCREMENT BY = allocationSize
CREATE SEQUENCE user_seq START 1 INCREMENT 50;

-- Hibernate вызывает:
SELECT nextval('user_seq');  -- 50 (блок 1-50)
SELECT nextval('user_seq');  -- 100 (блок 51-100)
```

> [!warning] **Если используем `SEQUENCE` для генерации**
> В скрипте создания таблицы НИКАК НЕ УКАЗЫВАЕМ стратегию генерации:
> ```sql
> CREATE SEQUENCE user_sequence START WITH 1 INCREMENT BY 50;
>CREATE TABLE user (
>    id BIGINT PRIMARY KEY,
>    -- другие поля
>);
> ```


----
#### [[@GenerationStrategy - JPA - Flashcards|Link to flashcards]]



---
### References:

