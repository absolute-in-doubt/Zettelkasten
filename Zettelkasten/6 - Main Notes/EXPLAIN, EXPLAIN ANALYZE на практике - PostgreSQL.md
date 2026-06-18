
14-04-2026 14:30

Status:

Tags: [[PostgreSQL DB]] [[Базы данных]]

---
# EXPLAIN, EXPLAIN ANALYZE на практике - PostgreSQL

```sql
EXPLAIN SELECT * FROM Bookings JOIN Tickets ON Bookings.book_ref = Tickets.book_ref WHERE Bookings.book_date > '2025-08-01 00:00:00' LIMIT 10;
```

```
QUERY PLAN
-----------------------------------------------------------------------------------
 Limit  (cost=0.86..2.03 rows=10 width=73)
   ->  Merge Join  (cost=0.86..349845.10 rows=2973597 width=73)
         Merge Cond: (bookings.book_ref = tickets.book_ref)
         ->  Index Scan using bookings_pkey on bookings  (cost=0.43..69777.65 rows=1292764 width=21)
               Filter: (book_date > '2025-08-01 00:00:00+03'::timestamp with time zone)
         ->  Index Scan using tickets_book_ref_passenger_id_outbound_key on tickets  (cost=0.43..239664.83 rows=2973894 width=52)
```


~={pink}Почему здесь идёт Index Scan по bookings_pkey, хотя фильтрация - по book_date?=~
	*В плане написано не `Index Cond`, а `Filter`:
    `Filter: (book_date > '2025-08-01 00:00:00+03'::timestamp with time zone)`
    Это значит, что ~={cyan}индекс `bookings_pkey` **используется как путь обхода строк=~**, а условие по `book_date` фильтруется потом, на уровне строк (`bookings`).*





----
#### [[EXPLAIN, EXPLAIN ANALYZE на практике - PostgreSQL - Flashcards|Link to flashcards]]



---
### References:

