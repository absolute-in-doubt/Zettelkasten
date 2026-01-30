
Theory for the cards: [[JPQL syntax - JPA]]

FILE TAGS: jpa

Q: Что делает оператор `SELECT` в JPQL?  
A: Оператор `SELECT` возвращает объекты сущностей или отдельные их поля по заданным условиям `FROM` и `WHERE`.
<!--ID: 1768296108347-->


Q: Как выбрать все объекты сущности `Entity` с определённым статусом в JPQL?  
A:  
```sql
SELECT e FROM Entity e
WHERE e.status = :status
```
<!--ID: 1768296108357-->


Q: Как выбрать только некоторые поля сущности в JPQL?  
A:   
```sql
SELECT e.id, e.name FROM Entity e
```
<!--ID: 1768296108364-->


Q: Как в JPQL собрать результат в DTO-объект?  
A:  
```sql
SELECT new com.example.dto.EntityDto(e.id, e.name)
FROM Entity e
```
<!--ID: 1768296108371-->


Q: Как выполнить простой `JOIN` между сущностью `Entity` и её связью `related` с фильтрацией по полю связи? (JPQL) 
A:  
```sql
SELECT e FROM Entity e JOIN e.related r WHERE r.active = true
```
<!--ID: 1768296108378-->


Q: Для чего используется `JOIN FETCH` в JPQL?  
A: `JOIN FETCH` используется для **eager**-загрузки связанной сущности в одном запросе, чтобы избежать ленивой подгрузки.
<!--ID: 1768296108386-->


Q: Как выглядит пример запроса с `JOIN FETCH`?  
A:   
```sql
SELECT e FROM Entity e JOIN FETCH e.related
```
<!--ID: 1768296108391-->


Q: Как выглядит bulk-`UPDATE` запрос в JPQL для изменения статуса сущности по её идентификатору?  
A:  
```sql
UPDATE Entity e SET e.status = :status WHERE e.id = :id
```
<!--ID: 1768296108399-->


Q: Какие особенности bulk-`UPDATE` и bulk-`DELETE` операций в JPQL?  
A: Они возвращают количество затронутых строк, не вызывают lifecycle callbacks и работают напрямую с БД, обходя контекст сущностей.
	
> [!warning]
> Модифицирующие JPQL‑запросы (`UPDATE`, `DELETE`) считаются bulk‑операциями и выполняются напрямую на уровне SQL. Они обновляют/удаляют данные **только в БД**, а уже загруженные и управляемые сущности в persistence context автоматически не меняются
<!--ID: 1768296108406-->


Q: Как выглядит bulk-`DELETE` запрос в JPQL (удалить `Entity` по  `status`)?  
A:  
```sql
DELETE FROM Entity e WHERE e.status = :status
```
<!--ID: 1768296108412-->


Q: Как отсортировать результат запроса по имени по возрастанию и дате по убыванию?  (JPQL)
A:  
```sql
SELECT e FROM Entity e ORDER BY e.name ASC, e.date DESC
```
<!--ID: 1768296108419-->


Q: Как сгруппировать сущности по статусу и посчитать их количество? (JPQL) 
A:  
```sql
SELECT e.status, COUNT(e) 
FROM Entity e 
GROUP BY e.status
```
<!--ID: 1768296108425-->


Q: Как отфильтровать группы по условию на агрегатную функцию `COUNT`?  
A:   
```sql
SELECT e.status, COUNT(e) 
FROM Entity e 
GROUP BY e.status
HAVING COUNT(e) > 10
```
<!--ID: 1768296108432-->


Q: Какие агрегатные функции доступны в JPQL?  
A: Доступны агрегатные функции `COUNT(e)`, `SUM(e.amount)`, `AVG(e.amount)`, `MAX(e.date)`, `MIN(e.date)`.
<!--ID: 1768296108438-->

