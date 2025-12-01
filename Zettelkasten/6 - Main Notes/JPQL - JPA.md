
26-11-2025 16:47

Status:

Tags: [[Hibernate & JPA]] [[Java+]]

---
# JPQL - JPA


# JPQL — синтаксис запросов

## 📌 SELECT
```sql
SELECT e FROM Entity e
WHERE e.status = :status
```

- Возвращает объекты Entity или их поля.
    
- Можно выбирать отдельные поля:
    

```sql
SELECT e.id, e.name FROM Entity e
```

- Можно собирать DTO:
    

```sql
SELECT new com.example.dto.EntityDto(e.id, e.name)
FROM Entity e
```

## 📌 JOIN

```sql
SELECT e FROM Entity e JOIN e.related r WHERE r.active = true
```

- `JOIN FETCH` для eager загрузки:
    

```sql
SELECT e FROM Entity e JOIN FETCH e.related
```

## 📌 UPDATE


```sql
UPDATE Entity e SET e.status = :status WHERE e.id = :id
```

- Bulk‑операция, возвращает количество обновлённых строк.
    
- Не вызывает lifecycle callbacks.
    

## 📌 DELETE


```sql
DELETE FROM Entity e WHERE e.status = :status
```

- Bulk‑операция, удаляет строки напрямую в БД.
    
- Не учитывает каскады и события.
    

## 📌 ORDER BY


```sql
SELECT e FROM Entity e ORDER BY e.name ASC, e.date DESC
```

## 📌 GROUP BY


```sql
SELECT e.status, COUNT(e) 
FROM Entity e 
GROUP BY e.status
```

## 📌 HAVING

```sql
SELECT e.status, COUNT(e) 
FROM Entity e 
GROUP BY e.status
HAVING COUNT(e) > 10
```

## 📌 Агрегатные функции

- `COUNT(e)`
    
- `SUM(e.amount)`
    
- `AVG(e.amount)`
    
- `MAX(e.date)`
    
- `MIN(e.date)`
    

## 📌 Подзапросы

```sql
SELECT e FROM Entity e 
WHERE e.amount > (SELECT AVG(x.amount) FROM Entity x)
```

## 📌 Параметры

- Именованные:
    

```sql
WHERE e.status = :status
```

- Позиционные:
    


```sql
WHERE e.status = ?1
```

----
#### [[JPQL - JPA - Flashcards|Link to flashcards]]



---
### References:

