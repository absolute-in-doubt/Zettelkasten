
Theory for the cards: [[FOREIGN KEY - SQL]]

FILE TAGS: sql

Q: Что произойдёт если удалить запись о Компании, на которую ссылается два пользователя?
```sql
CREATE TABLE Users (
    id INTEGER,
    name VARCHAR(255) NOT NULL,
    age INTEGER NOT NULL DEFAULT 18,
    company INTEGER,
    PRIMARY KEY (id),
    FOREIGN KEY (company) REFERENCES Companies (id)
);
```
A: Компания не будет удалена, т.к. по умолчанию используется `ON DELETE RESTRICT`, который запрещает удаление записи, если на нёё ссылается хоть одна запись из других таблиц.
<!--ID: 1761581631036-->

Q: Какие есть виды каскадирования для связей (FK) в SQL?
A:  `ON UPDATE/DELETE` 
- `CASCADE` - если Parent запись меняется/удаляется -> связанная запись в Child таблице тоже меняется (обновляется значения поля FK)/удаляется
	
- `SET NULL` - если Parent запись меняется/удаляется -> в связанной Child записи значение поля с FK становится NULL
	
- `SET DEFAULT` - если Parent запись меняется/удаляется ->в связанной Child записи значение поля с FK становится дефолтным
	
- `RESTRICT` ~ `NO ACTION` - (**used by default in Postgres**) если пытаемся изменить/удалить Parent запись, на которую ссылаются какие-то записи из Child table -> получаем ошибку:
```sql
Cannot delete or update a parent row: a foreign key constraint fails
```
	
Триггерятся в случае если запись в Parent таблице (чье поле мы храним) gets updated/deleted.
<!--ID: 1769253674995-->


