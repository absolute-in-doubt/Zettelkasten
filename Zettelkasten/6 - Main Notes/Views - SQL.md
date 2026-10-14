
17-09-2025 12:47

Status: #child 

Tags: [[SQL]]

---
# Views - SQL

View (представление) - это "виртуальные таблицы" для просмотра информации из БД. Они конструируются с помощью SELECT запросов. И при обновлении информации в БД, она обновляется также и в View.

#### Для чего нужны

This can be useful, for example, if your company's policy does not allow everyone to use confidential user information. Therefore, instead of allowing direct access to the user table (Users), you define a view named ViewUsers and require everyone to use it to access user data.


### Синтаксис


#### General syntax:
```sql
CREATE [OR REPLACE] VIEW view_name [(view_column_names)]
AS select_expression
```

~={cyan}OR REPLACE=~ - при использовании этого параметра, ~={orange}если=~ View уже существует, он~={orange} будет удалён и будет создан новый=~.  Иначе, ~={green}без=~ использования ~={cyan}OR REPLACE=~, ~={green}если=~ View с таким именем уже ~={green}существует=~ - ~={green}будет ошибка=~.

#### Пример:
```sql
CREATE VIEW ViewUsers AS
    SELECT id,
           name,
           CONCAT(SUBSTR(email, 1, 2), '****', RIGHT(email, 4)) AS email
FROM Users;
```




---
### VIEW vs MATERIALIZED VIEW


### Обычный VIEW

Обычное представление хранит только **текст запроса**. Данных у него нет: при каждом обращении запрос подставляется в вышестоящий и выполняется заново.


```sql
CREATE VIEW active_users AS
SELECT id, name, last_login
FROM users
WHERE is_active;

SELECT * FROM active_users WHERE id = 42;
```

**Плюсы:**

- всегда актуальные данные;
- не занимает места на диске;
- планировщик может оптимизировать итоговый запрос целиком (в том числе пробросить условия внутрь);
- простые представления можно использовать для `INSERT/UPDATE/DELETE` (auto-updatable views).

**Минусы:** тяжёлый запрос (агрегации, джойны больших таблиц) будет выполняться каждый раз.


### MATERIALIZED VIEW

Материализованное представление **физически хранит результат запроса** как таблица. Читается быстро, но данные «замораживаются» до явного обновления.


```sql
CREATE MATERIALIZED VIEW sales_by_day AS
SELECT date_trunc('day', created_at) AS day,
       count(*)                      AS orders,
       sum(amount)                   AS revenue
FROM orders
GROUP BY 1;
```

Можно создать «пустым», без первоначального наполнения:

```sql
CREATE MATERIALIZED VIEW sales_by_day AS ... WITH NO DATA;
```

В таком состоянии из него нельзя читать, пока не выполнен первый `REFRESH`.

#### Обновление данных


```sql
REFRESH MATERIALIZED VIEW sales_by_day;
```

Важно: Postgres **не обновляет** matview автоматически. Нужно делать это самому: через cron, `pg_cron`, планировщик приложения или триггеры/внешние механизмы.

Обычный `REFRESH` берёт блокировку **`ACCESS EXCLUSIVE`**: пока идёт обновление, читать из представления нельзя. Для тяжёлых запросов это может быть проблемой.

#### REFRESH ... CONCURRENTLY

```sql
REFRESH MATERIALIZED VIEW CONCURRENTLY sales_by_day;
```

Читатели не блокируются: Postgres строит новый результат во временной таблице, сравнивает с текущим и применяет разницу. Условия:

- у matview должен быть **`UNIQUE` индекс** (без `WHERE`, по столбцам, не по выражениям);
- представление уже должно быть наполнено;
- работает медленнее обычного `REFRESH` и создаёт больше нагрузки.

```sql
CREATE UNIQUE INDEX ON sales_by_day (day);
```

#### Индексы

Поскольку matview хранит реальные данные, на нём **можно создавать индексы**, чего нельзя для обычного VIEW:

```sql
CREATE INDEX ON sales_by_day (revenue);
```

Индексы сохраняются после `REFRESH`.

#### Прочее

```sql
ALTER MATERIALIZED VIEW sales_by_day RENAME TO daily_sales;
ALTER MATERIALIZED VIEW sales_by_day SET TABLESPACE fast_ssd;
DROP MATERIALIZED VIEW sales_by_day;
```

Посмотреть список и состояние: `pg_matviews` (поле `ispopulated` показывает, наполнено ли оно).



```sql
SELECT schemaname, matviewname, ispopulated FROM pg_matviews;
```

### Сравнение

| |VIEW|MATERIALIZED VIEW|
|---|---|---|
|Хранит данные|нет|да|
|Актуальность|всегда свежие|на момент последнего REFRESH|
|Скорость чтения|как у исходного запроса|как у таблицы|
|Индексы|нет|да|
|Место на диске|нет|да|
|Изменение данных через него|иногда возможно|нет|
|Обновление|не нужно|вручную|

### Ограничения matview

- Нет автоматического и **инкрементального** обновления: `REFRESH` всегда пересчитывает весь запрос (в отличие, например, от Oracle или расширения `pg_ivm`, которое добавляет инкрементальное обновление).
- Нельзя делать `INSERT/UPDATE/DELETE` напрямую.
- Нет поддержки row-level security на самом matview (права выдаются через `GRANT` как на таблицу).
- Нельзя использовать временные объекты (temporary tables/functions) в определении запроса.

### Когда что использовать

**VIEW** подходит для:

- инкапсуляции сложных запросов и упрощения доступа;
- ограничения видимости столбцов/строк (в связке с правами);
- когда нужны всегда свежие данные.

**MATERIALIZED VIEW** подходит для:

- отчётов и дашбордов с тяжёлыми агрегациями;
- кэширования результатов дорогих запросов;
- данных, для которых допустима задержка (раз в час, раз в сутки).

----
#### [[Views - SQL - Flashcards|Link to flashcards]]



---
### References:

