
22-11-2025 10:22

Status:

Tags: [[Timescale DB]] [[Базы данных]]

---
# Создание HyperTable - Timescale DB

```sql
CREATE TABLE conditions ( 
	"time" TIMESTAMPTZ NOT NULL, 
	location TEXT NOT NULL, 
	device TEXT NOT NULL, 
	temperature DOUBLE PRECISION NULL, 
	humidity DOUBLE PRECISION NULL 
) WITH (
	tsdb.hypertable, 
	tsdb.chunk_interval='1 day' 
);
```



---
## Создание из обычной таблицы

```sql
CREATE TABLE income_loss_period (
  period_start TIMESTAMPTZ NOT NULL,
  period_end   TIMESTAMPTZ NOT NULL,
  value        NUMERIC
);
SELECT create_hypertable('income_loss_period', 'period_start');
```


#### Про функцию `create_hypertable`:

```sql
SELECT create_hypertable(
    'table_name',          -- имя таблицы
    'time_column',         -- колонка времени
    chunk_time_interval => interval,   -- (опционально) размер чанка
    partitioning_column => 'colname',  -- (опционально) второе измерение
    number_partitions => N             -- (опционально) количество партиций
);
```

---
## Изменение размера chunk-ов:

```sql
SELECT set_chunk_time_interval('conditions', INTERVAL '24 hours');
```

Обновленный `chunk_interval` применяется ТОЛЬКО К НОВЫМ chunks, которые будут созданы после этого. 

----
#### [[Создание HyperTable - Timescale DB - Flashcards|Link to flashcards]]



---
### References:

