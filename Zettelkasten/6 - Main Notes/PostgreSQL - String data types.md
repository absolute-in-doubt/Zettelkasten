
17-09-2025 11:05

Status: #child

Tags: [[SQL]] [[PostgreSQL DB]]

---
# PostgreSQL - String data types

| Type                                                                                                  | Description                                                 | Range of characters                       |
| :---------------------------------------------------------------------------------------------------- | :---------------------------------------------------------- | :---------------------------------------- |
| ~={cyan}CHAR(n)=~    Содержит строки фиксированной длины. Заполняется пробелами до указанной длины. . | Любая длина от 1 до 10,485,760                              |                                           |
| ~={cyan}VARCHAR(n)=~                                                                                  | Содержит строки изменяемой длины с ограничением макс. длины | Любая длина от 1 до 10,485,760            |
| ~={cyan}TEXT=~                                                                                        | Неограниченная длина                                        | Practically unlimited length (up to 1 GB) |

Плюс использования TEXT - убираем ответственность проверки CONSTRAINTS с БД. Сейчас принято делать dummy DB, вся логика в т.ч. проверки - в самом приложении.
	
> [!warning] 
> Если делаем текстовое поле индексом, то поиск по regex будет работать НЕ ЧЕРЕЗ ИНДЕКС. 
> Чтобы работало через индекс надо указывать text_pattern_ops:
> ```sql
> -- Таблица пользователей
>CREATE TABLE users (
>    id SERIAL PRIMARY KEY,
>    name TEXT NOT NULL
>);
>
>-- Обычный индекс (может не работать с LIKE в ru_RU.UTF-8 локали)
>CREATE INDEX idx_users_name_default ON users (name);
>
>-- Специальный индекс для LIKE 'префикс%'
>CREATE INDEX idx_users_name_pattern ON users (name text_pattern_ops);
> ```
> 
	
---
**Полнотекстовый поиск:**
(обычно для этого используют отдельные движки типа Elasticsearch, но Postgres тоже поддерживает эту возможность)
	
- tsvector - тип для хранения текста, по которому в будущем делаем полнотекстовый поиск
	
- tsquery - тип в который преобразуем запрос



----
#### [[PostgreSQL - String data types - Flashcards|Link to flashcards]]



---
### References:

- [[Хранение данных - PostgreSQL#TOAST - The Oversized Attribute Storage Technique]]