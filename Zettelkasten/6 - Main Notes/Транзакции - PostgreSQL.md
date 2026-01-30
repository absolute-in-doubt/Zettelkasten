
24-01-2026 17:07

Status:

Tags: [[PostgreSQL DB]] [[SQL]]

---
# Транзакции - PostgreSQL

Транзакции могут выполняться параллельно. За параллельное выполнение транзакций в БД Postgres отвечает многоверсионная модель. Эта модель предполагает создание снимка или копии базы данных, с которым и работает транзакция.

Транзакции обладают свойствами [[ACID principles - БД|ACID]].


### Установка уровня изоляции при транзакции

```sql
BEGIN ISOLATION LEVEL READ COMMITTED; ~ BEGIN; 
--т.к это default isolation level for Postgres
...
COMMIT;
```

```SQL
BEGIN TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```


----
#### [[Транзакции - PostgreSQL - Flashcards|Link to flashcards]]



---
### References:

