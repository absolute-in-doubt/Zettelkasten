
24-01-2026 17:01

Status:

Tags: [[SQL]]

---
# Optimistic blocking - SQL


Предполагает отсутствие конфликтов, проверяет в конце.

```sql
-- Читаем с версией/таймстампом
SELECT balance, version FROM accounts WHERE id = 1;  -- version=5

-- В приложении: new_balance = balance - 100
-- В конце:
UPDATE accounts 
SET balance = new_balance, version = version + 1 
WHERE id = 1 AND version = 5;  -- если version изменился, конфликт!

-- Если 0 строк обновлено → retry транзакции
```

Использует версии строк или MVCC. Эффективно при редких конфликтах.

----
#### [[Optimistic blocking - SQL - Flashcards|Link to flashcards]]



---
### References:

