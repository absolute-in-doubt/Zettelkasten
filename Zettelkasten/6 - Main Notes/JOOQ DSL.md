
07-10-2026 13:26

Status:

Tags: [[JOOQ]] [[Java+]]

---
# JOOQ DSL


### 1. Основные компоненты

- **`DSLContext`** — основной интерфейс для выполнения запросов, транзакций и взаимодействия с базой данных (обычно создается через `DSL.using(...)`).


- **Статический класс `DSL`** — содержит фабричные методы для построения условий, агрегатных функций (`count`, `max`, `coalesce`) и подзапросов.


- **Сгенерированные классы** — Java-обёртки над таблицами и полями БД, созданные кодогенератором (например, `Tables.BOOK`, `Book.TITLE`).


---
### 2. Базовые операции (CRUD)

#### SELECT (Выборка данных)

```java
Result<Record> result = dsl.select(BOOK.TITLE, BOOK.AUTHOR_ID)
    .from(BOOK)
    .where(BOOK.PUBLISHED_IN.greaterThan(2010))
    .fetch();
```


#### INSERT (Вставка данных)

```java
dsl.insertInto(BOOK, BOOK.TITLE, BOOK.AUTHOR_ID)
   .values("Java 17 in Action", 1)
   .execute();
```


#### UPDATE (Обновление данных)

```java
dsl.update(BOOK)
   .set(BOOK.TITLE, "Advanced jOOQ")
   .where(BOOK.ID.eq(5))
   .execute();
```

#### DELETE (Удаление данных)

```java
dsl.deleteFrom(BOOK)
   .where(BOOK.ID.eq(5))
   .execute();
```

### 3. Ключевые особенности и преимущества

- **Типобезопасность:** Опечатки в названиях колонок или несовпадение типов данных ловятся компилятором Java, а не падают в рантайме.
    
      
    
- **Сложные SQL-конструкции:** Поддерживает `JOIN`, `GROUP BY`, `HAVING`, оконные функции (Window Functions), CTE (`WITH`) и UNION «из коробки».
    
      
    
- **Интеграция со Spring Boot:** Автоматически настраивает бин `DSLContext` при подключении `jooq-spring-boot-starter`.


----
#### [[JOOQ DSL - Flashcards|Link to flashcards]]



---
### References:

