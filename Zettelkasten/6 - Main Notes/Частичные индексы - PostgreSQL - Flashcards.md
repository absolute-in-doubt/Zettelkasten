
Theory for the cards: 

FILE TAGS: postgres

Q: Что такое частичные индексы? Для чего они нужны?
A: Частичный индекс (partial index) создаётся только для строк, удовлетворяющих условию WHERE, что экономит место и ускоряет запросы по редким значениям.
	
**PostgreSQL**
	
Индекс на неоплаченные счета (где их мало):
	
```sql
CREATE TABLE invoices (
    id SERIAL PRIMARY KEY,
    amount DECIMAL,
    status VARCHAR(20)  -- 'paid', 'unpaid'
);
	
-- Частичный индекс только для unpaid (5% строк)
CREATE INDEX idx_invoices_unpaid ON invoices (status, amount) 
WHERE status = 'unpaid';
```
	
Запрос `SELECT * FROM invoices WHERE status = 'unpaid' ORDER BY amount` использует индекс. Для `status = 'paid'` — полный скан.
	
**Уникальный частичный индекс**
	
Разрешает множественные неудачные попытки теста, но одну успешную:
	
```sql
CREATE TABLE test_results (
    user_id INT,
    subject VARCHAR(50),
    target_topic VARCHAR(50),
    result BOOLEAN  -- true = success
);
	
-- Только одна успешная запись на user+subject+topic
CREATE UNIQUE INDEX idx_test_success ON test_results (user_id, subject, target_topic)
WHERE result = true;
```
	
Вставка двух `result=true` для одного пользователя заблокируется, `result=false` — пройдёт.
	
	
---
**Сравнение с обычным индексом**
	
|Тип|Размер|Скорость SELECT (редкие значения)|
|---|---|---|
|Полный|100% строк|Медленнее|
|Частичный|5-10% строк|5-10x быстрее|
<!--ID: 1778246479577-->

