
Theory for the cards: [[EXPLAIN, EXPLAIN ANALYZE на практике - PostgreSQL]]

FILE TAGS: postgres explain_analyze

Q: Почему здесь идёт Index Scan по bookings_pkey, хотя фильтрация - по book_date?
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
A: *В плане написано не `Index Cond`, а `Filter`:
`Filter: (book_date > '2025-08-01 00:00:00+03'::timestamp with time zone)`
Это значит, что ~={cyan}индекс `bookings_pkey` **используется как путь обхода строк=~**, а условие по `book_date` фильтруется потом, на уровне строк (`bookings`).*
<!--ID: 1776166560327-->

Q: Какой здесь будет тип сканирования?
```
   Столбец    |      Тип      | Модификаторы |         Описание
--------------+---------------+--------------+---------------------------
 book_ref     | char(6)       | not null     | Номер бронирования
 book_date    | timestamptz   | not null     | Дата бронирования
 total_amount | numeric(10,2) | not null     | Полная сумма бронирования
```
	
```sql
CREATE INDEX idx_book_date_book_ref ON bookings(book_date) INCLUDE (book_ref);
```
	
Запрос:
```sql
SELECT book_ref, book_date FROM bookings WHERE book_date > '2025-01-01' AND book_date < '2025-09-01';
```
A: Т.к. тут индекс по полю, по которому производится фильтрация, и поля, которые требуются в представлении, есть как неключевые столбцы индекса, ЕСЛИ **Visibility Map доказывает**, что не нужно ходить в таблицу за дополнительной MVCC‑информацией => будет `Index Only Scan`:
	
```sql
    QUERY PLAN
-----------------------------------------------------------------------------------
 Index Only Scan using idx_book_date_book_ref on bookings  (cost=0.43..8.45 rows=1 width=15)
   Index Cond: ((book_date > '2025-01-01 00:00:00+03'::timestamp with time zone) AND (book_date < '2025-09-01 00:00:00+03'::timestamp with time zone))
```
<!--ID: 1776174148924-->
