
23-03-2026 20:31

Status:

Tags: [[PostgreSQL DB]] [[System Design]]

---
# Реализация партиционирования - PostgreSQL

Пример партиционирования по диапазону (Range Partitioning):

```sql
CREATE TABLE users (
    id SERIAL,    
    username VARCHAR(50),    
    email VARCHAR(100),    
    created_at TIMESTAMP NOT NULL) PARTITION BY RANGE (created_at);
    
-- Партиция для данных за январь 2023 года
CREATE TABLE users_2023_01 PARTITION OF users    
	FOR VALUES FROM ('2023-01-01') TO ('2023-02-01');
	
-- Партиция для данных за февраль 2023 года
CREATE TABLE users_2023_02 PARTITION OF users    
	FOR VALUES FROM ('2023-02-01') TO ('2023-03-01');

-- Партиция для данных за март 2023 года
CREATE TABLE users_2023_03 PARTITION OF users    
	FOR VALUES FROM ('2023-03-01') TO ('2023-04-01');
```

**Пример запроса**

При вставке данных PostgreSQL автоматически направляет строки в соответствующую партицию на основе значения столбца `created_at`:

```sql
INSERT INTO users (username, email, created_at)VALUES ('user1', 'user1@example.com', '2023-01-15');
```

#### Пример запроса

При выполнении запросов PostgreSQL автоматически выбирает нужные партиции на основе условий. Например:

```sql
SELECT * FROM users WHERE created_at BETWEEN '2023-02-01' AND '2023-03-01';
```

В этом случае PostgreSQL будет искать данные только в партиции `users_2023_02`.




----
#### [[Реализация партиционирования - PostgreSQL - Flashcards|Link to flashcards]]



---
### References:

- [[Партиционирование]]