
Theory for the cards: [[@GenerationStrategy - JPA]]

FILE TAGS: jpa

Q: Почему GenerationStrategy.SEQUENCE является наиболее предпочтительным? Как контролируется потокобезопасность при доступе к SEQUENCE в БД?
A: Потому что он работает с отдельным объектом sequence в БД и не требует блокировок -> выше скорость вставки. 
	
**SEQUENCE в PostgreSQL/Oracle потокобезопасна на уровне БД** — `nextval()` выполняется **атомарно без SELECT FOR UPDATE**. БД сама гарантирует уникальность без блокировок.
	
Также позволяет улучшить вставку батчами, инкрементируя SEQUENCE на размер батча:
	
```sql
-- наш SEQUENCE:
CREATE SEQUENCE user_sequence START 1 INCREMENT 50;
-- тут 50 - размер батча
```
	
Выполнение метода `EntityManager` (сохраняем User-ов из списка users (200 штук)):
	
```java
userRepository.saveAll(users);
```
	
```sql
как с ним работает Hibernate при массовой вставке
	
-- 1. Резерв блока ID (allocationSize)
SELECT nextval('user_sequence');  -- 50
	
-- 2. Батч INSERT (batch_size=50) с ID 1-50
INSERT INTO user (id, name) VALUES (1, 'User0'), (2, 'User1'), ... (50, 'User49')
	
-- 3. Следующий блок ID
SELECT nextval('user_sequence');  -- 100
	
-- 4. Второй батч 51-100
INSERT INTO user (id, name) VALUES (51, 'User50'), ... (100, 'User99')
	
-- И так далее...
```
	
Таким образом SEQUENCE позволяет избежать обращения к БД для генерации 50 id при вставке 50 User-ов. И команда выполнится за один запрос к SEQUENCE + один INSERT.
<!--ID: 1769501672700-->




Q: Приведи пример создания SEQUENCE в БД и привязки её к таблице (с помощью JPA). Как производится запрос нового значения из такой SEQUENCE (SQL query)? Как установить размер батчей для генерации id батчами?
A:   
```sql
CREATE SEQUENCE user_sequence START 1 INCREMENT 50;
-- тут 50 - размер батча
```
	
Его использование на entity классе:
	
```java
@Id
@SequenceGenerator(
	name = "user_seq", 
	sequenceName = "user_sequence", 
	allocationSize = 50 //тут также прописываем размер батчей
)
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "user_seq")
private Long id;
```
	
**При `allocationSize=50`**:
	
1. Hibernate вызывает `nextval('user_seq')` → получает `100`
    
2. Локально генерирует ID: `100, 101, 102...149` **без обращений к БД**
    
3. Следующий блок: `nextval()` → `150`, затем `150-199`
	
```sql
-- Hibernate вызывает:
SELECT nextval('user_seq');  -- 50 (блок 1-50)
SELECT nextval('user_seq');  -- 100 (блок 51-100)
```
<!--ID: 1769501672710-->


Q: Какие вы помните стратегии генерации id в Hibernate JPA?
A:  
	
| Стратегия  | Описание                                                | БД-поддержка                  | Пример                                                                                          |
| ---------- | ------------------------------------------------------- | ----------------------------- | ----------------------------------------------------------------------------------------------- |
| `AUTO`     | Автовыбор провайдером (часто `IDENTITY` или `SEQUENCE`) | Все                           | `@GeneratedValue(strategy = GenerationType.AUTO)`                                               |
| `IDENTITY` | Автоинкремент БД (INSERT → SELECT LAST_INSERT_ID)       | MySQL, PostgreSQL, SQL Server | `@GeneratedValue(strategy = GenerationType.IDENTITY)`                                           |
| `SEQUENCE` | БД-sequence (nextval перед INSERT)                      | Oracle, PostgreSQL, H2, DB2   | `@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "seq")` с `@SequenceGenerator` |
| `TABLE`    | Отдельная таблица с счётчиками (SELECT FOR UPDATE)      | Все                           | `@GeneratedValue(strategy = GenerationType.TABLE)`                                              |
<!--ID: 1769502015349-->
