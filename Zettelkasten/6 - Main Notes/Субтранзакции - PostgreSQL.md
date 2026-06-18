
14-02-2026 15:50

Status:

Tags: [[PostgreSQL DB]] 

---
# Субтранзакции - PostgreSQL


**~={red}bad practice=~**

Субтранзакции (subtransactions) в PostgreSQL — это вложенные транзакции внутри основной, позволяющие разбивать крупные операции на мелкие части для удобной обработки ошибок.

## Определение и назначение

Подтранзакции запускаются внутри родительской транзакции и образуют иерархическое дерево. Они фиксируются (COMMIT) или откатываются (ROLLBACK) независимо, не затрагивая родителя, что упрощает обработку исключений в коде.

## Создание

- Явно через `SAVEPOINT имя_точки;`, затем `RELEASE SAVEPOINT` (фиксация) или `ROLLBACK TO SAVEPOINT` (откат).
    
- Автоматически в PL/pgSQL через блок `EXCEPTION`, в PL/Python или PL/Tcl.
    

## Идентификаторы

- Subxid — уникальный ID для подтранзакций с записью (read-only не получают).
    
- Родительский xid всегда меньше дочерних; связи хранятся в `pg_subtrans`.
    
- При фиксации подтранзакции все её дочерние тоже фиксируются; при откате — откатываются.
    

## Особенности поведения

Фиксация верхней транзакции сохраняет все подтранзакции в `pg_xact`; откат верхней отменяет всё дерево. Поддержка вложенности любой глубины, но с лимитом кэша (64 subxid на сессию).

### Пример 

```sql
BEGIN;

-- Первая операция
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
SELECT * FROM accounts;  -- balance: 900, 2000

-- Создаем первую точку сохранения
SAVEPOINT sp1;

-- Вторая операция (может провалиться)
UPDATE accounts SET balance = balance - 3000 WHERE id = 2;  -- Недостаточно средств? Нет проверки, но для примера

-- Откатываем только до sp1
ROLLBACK TO SAVEPOINT sp1;
SELECT * FROM accounts;  -- balance: 900, 2000 (вторая операция отменена)

-- Создаем вторую точку
SAVEPOINT sp2;

UPDATE accounts SET balance = balance - 500 WHERE id = 2;
SELECT * FROM accounts;  -- balance: 900, 1500

-- Подтверждаем вторую точку
RELEASE SAVEPOINT sp2;

-- Финальная фиксация всей транзакции
COMMIT;
SELECT * FROM accounts;  -- balance: 900, 1500 (все сохранено)
```


## Ключевые команды

- `SAVEPOINT имя` — создать точку.
    
- `RELEASE SAVEPOINT имя` — подтвердить (commit подтранзакции).
    
- `ROLLBACK TO SAVEPOINT имя` — откат до точки.
    
- `RELEASE ALL SAVEPOINT` — подтвердить все точки.

----
#### [[Субтранзакции - PostgreSQL - Flashcards|Link to flashcards]]



---
### References:

