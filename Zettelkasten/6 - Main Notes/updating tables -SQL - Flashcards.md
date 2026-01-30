
Theory for the cards: [[updating tables -SQL]]

FILE TAGS: sql

Q: Как переименовать колонку существующей таблицы?
A: `ALTER` + `RENAME COLUMN`:
```sql
ALTER TABLE table_name
RENAME COLUMN old_column_name TO new_column_name;
```
<!--ID: 1769252490004-->

Q: Как связать поле user_id существующей таблицы sessions с полем id таблицы users?
A:   
```sql
ALTER TABLE sessions
ADD CONSTRAINT fk_user FOREIGN KEY (ser_id)
REFERENCES users(id);
```
<!--ID: 1769252776923-->

Q: Как к существующей таблице people добавить ограничение уникальности по полю name?
A:   
```sql
ALTER TABLE people
ADD CONSTRAINT unique_name UNIQUE (name);
```
<!--ID: 1769252840883-->
