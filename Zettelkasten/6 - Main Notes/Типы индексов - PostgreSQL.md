
18-06-2026 19:31

Status:

Tags: [[PostgreSQL DB]] [[Базы данных]]

---
# Типы индексов - Postgres


## 1. B-tree (B-дерево) — стандартный индекс



```sql
CREATE INDEX idx_users_email ON users(email);
```

## Когда использовать:

|Оператор|Подходит|
|---|---|
|`=`, `<>`|✅|
|`<`, `>`, `<=`, `>=`|✅|
|`BETWEEN`, `IN`|✅|
|`LIKE 'pattern%'` (начало с префикса)|✅|
|`LIKE '%pattern'` (suffix)|❌|
|`ORDER BY`|✅ (если сортировка по тому же столбцу)|
|`MIN()`, `MAX()`|✅|

## Особенности:

- **По умолчанию** при `CREATE INDEX`
    
- Сбалансированное дерево: все пути к листьям одинаковой длины
    
- **O(log N)** для поиска, вставки, удаления
    
- Занимает ~10-30% от размера таблицы
    
- Поддерживает **unique index**: `CREATE UNIQUE INDEX`
    
- **Composite index**: `CREATE INDEX ON tbl(col1, col2, col3)`


## Composite index правила:

```sql
-- Порядок столбцов важен!
CREATE INDEX ON orders(customer_id, created_at);  -- ✅
-- WHERE customer_id = ? AND created_at > ?  → использует индекс
-- WHERE created_at > ?  → НЕ использует (leftmost prefix rule)
```

---

## 2. Hash индекс


```sql
CREATE INDEX idx_users_email ON users USING hash(email);
```

## Когда использовать:

|Оператор|Подходит|
|---|---|
|`=`|✅ (только равно!)|
|`<`, `>`, `BETWEEN`|❌|
|`LIKE`|❌|
|`ORDER BY`|❌|

## Особенности:

- **Только для равенства** (`=`)
    
- **O(1)** для поиска (идеальная хеш-таблица)
    
- Не поддерживает range queries
    
- **Не WAL-safe** в старых версиях (PostgreSQL 10+ исправлено)
    
- Занимает меньше места чем B-tree
    
- **Не рекомендуется** для большинства случаев (B-tree быстрее в 99% кейсов)


---

## 3. GiST (Generalized Search Tree)

```sql
CREATE INDEX idx_geo ON locations USING gist(coordinates);
CREATE INDEX idx_text ON documents USING gist(content)
  WITH (opclass='text_gist_ops');
```

## Когда использовать:

|Тип данных|Операторы|
|---|---|
|**Геоданные** (POINT, LINE, POLYGON)|`&&` (overlap), `@>` (contains), `<@` (contained)|
|**Текст** (full-text search)|`@@` (match), `~` (regex)|
|**Изображения**, **сетевые адреса**|финализирует сходство|

## Особенности:

- **Сбалансированное дерево** (как B-tree)
    
- Поддерживает **семантический поиск** (не точное равенство)
    
- Каждый узел хранит **диапазон/область** данных
    
- **Меньше точный**, но **быстрее для сложных запросов**
    
- Может давать **false positives** → требует проверки строки
    

---

## 4. GIN (Generalized Inverted Index)


```sql
CREATE INDEX idx_json ON users USING gin(metadata);
CREATE INDEX idx_tags ON posts USING gin(tags);
CREATE INDEX idx_text ON documents USING gin(content);
```

## Когда использовать:

|Тип данных|Операторы|
|---|---|
|**JSONB**|`@>` (contains), `?` (has key), `?\|` (has any key)|
|**Массивы** (ARRAY)|`@>` (contains), `&&` (overlap), `=`|
|**Full-text search**|`@@` (match)|

## Особенности:

- **Обратный индекс**: каждый ключ → список строк
    
- **Лучший для JSONB и массивов**
    
- `@>` (contains) → **идеально** с GIN
    
- Занимает **больше места** чем GiST (3-10x от таблицы)
    
- **Медленнее в INSERT/UPDATE** (нужно обновлять много записей)
    
- Поддерживает **fastupdate**: быстрая вставка, но требует periodic `REINDEX`
    

## Пример JSONB:

```sql
-- Таблица
CREATE TABLE users (id INT, metadata JSONB);

-- Поиск
SELECT * FROM users WHERE metadata @> '{"role": "admin"}';  -- ✅ использует GIN

-- GIN vs GiST для JSONB:
-- GIN: точнее, больше места, медленнее INSERT
-- GiST: меньше места, быстрее INSERT, может быть false positive
```

---

## 5. SP-GiST (Space-Partitioned GiST)


```sql
CREATE INDEX idx_quad ON points USING spgist(coordinates);
```

## Когда использовать:

|Тип данных|Примеры|
|---|---|
|**Геоданные** (квадратные деревья)|POINT, POLYGON|
|**Близость** (nearest neighbor)|`ORDER BY distance LIMIT 1`|
|**Терминальные данные** с двоичным разбиением|телефонные номера, IP|

## Особенности:

- **Partitioned space tree** (как GiST, но с двоичным разбиением)
    
- Оптимизирован для **nearest neighbor search**
    
- **Быстрее GiST** для specific кейсов (гео, близость)
    
- Меньше распространен, знать основы
    

---

## 6. BRIN (Block-Range INdex)


```sql
CREATE INDEX idx_created ON orders USING brin(created_at);
```

## Когда использовать:

|Сценарий|Пример|
|---|---|
|**Таблицы с миллиардами строк**|~1B+ rows|
|**Данные с естественной сортировкой**|`created_at`, `timestamp`|
|**Только для range queries**|`WHERE created_at > ?`|
|**Минимум места**|~1% от размера таблицы|

## Особенности:

- **Индекс по блокам** (не по строкам): каждый блок → мин/макс значение
    
- **Минимальный размер** (10-100x меньше B-tree)
    
- **Медленнее для точного поиска** (`=`)
    
- Идеален для **time-series данных**
    
- `WHERE` клиайт: частичный индекс
    

```sql
-- Частичный BRIN 
CREATE INDEX ON orders USING brin(created_at)   
WHERE status = 'pending';  -- только pending заказы
```

---

## Сравнительная таблица

|Тип|Размер|INSERT|Поиск|Range|Специфика|
|---|---|---|---|---|---|
|**B-tree**|10-30%|Быстрый|O(log N)|✅|Стандарт, универсальный|
|**Hash**|5-15%|Быстрый|O(1)|❌|Только `=`|
|**GiST**|20-40%|Средний|O(log N)|⚠️|Гео, текст, семантика|
|**GIN**|30-100%|Медленный|O(log N)|✅|JSONB, массивы|
|**SP-GiST**|15-30%|Средний|O(log N)|✅|Гео, nearest neighbor|
|**BRIN**|1-5%|Быстрый|O(N)|✅|Time-series, миллиарды строк|

---


## 1. Как выбрать индекс?


```
1. Oператоры в WHERE? (=, <>, <, >, LIKE, @>, &&)
2. Тип данных? (text, int, jsonb, array, geo)
3. Размер таблицы? (< 1M, 1M-100M, > 1B)
4. Частота INSERT/UPDATE? (high, medium, low)
5. Нужен ли ORDER BY? (да → B-tree)
```

## 2. Когда индекс НЕ используется?

- Таблица **меньше индекса** (plannner выбирает sequential scan)
    
- **WHERE col IS NULL** (если nulls редкие → индекс работает, если частые → не работает)
    
- **Функция в WHERE** без `STABLE` (например, `WHERE LOWER(name) = 'test'` → нужен индекс на `LOWER(name)`)
    
- **Ограниченное количество строк** (< 1% таблицы → sequential scan быстрее)
    

## 3. Как проверить использование индекса?

```sql
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'test@example.com';
-- Смотрим: "Index Scan using idx_users_email" vs "Seq Scan"
```

## 4. Что такое covering index?

Это индекс, который позволяет делать Index Only scan.

```sql
-- Индекс включает все нужные колонки → не нужно читать таблицу
CREATE INDEX ON orders(customer_id, created_at, total);
SELECT customer_id, created_at, total FROM orders WHERE customer_id = 1;
-- ✅ Index Only Scan (не читает таблицу)
```

## 5. Partial index (частичный индекс)?

```sql
CREATE INDEX ON orders(status, created_at) WHERE status != 'cancelled';
-- Индекс только для активных заказов → меньше места, быстрее
```

## 6. Concurrent создание индекса? **~={red}???=~**

```sql
CREATE INDEX CONCURRENTLY idx ON tbl(col);
-- Не блокирует INSERT/UPDATE/DELETE (но блокирует DROP TABLE)
-- Требует 2 pass: build + finalize
```

## 7. Что делать с разросшимся индексом?

```sql
REINDEX INDEX idx_name;              -- Пересоздать индекс
REINDEX TABLE table_name;            -- Пересоздать все индексы таблицы
REINDEX CONCURRENTLY idx_name;       -- Без блокировки (PostgreSQL 12+)
```


----
#### [[Типы индексов - Postgres - Flashcards|Link to flashcards]]



---
### References:

