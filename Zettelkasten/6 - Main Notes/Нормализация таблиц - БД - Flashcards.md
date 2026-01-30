
Theory for the cards: [[Нормализация таблиц - БД]] 

FILE TAGS: databases

Q: Расскажи о нормальных формах (хотя бы до 3NF). Чем они отличаются? В чём преимущества их использования?
A:   
**1NF**
	
- Каждая строка имеет свой ключ
- Все поля содержат atomic values. Т.е. нет такого, что в поле хранится список/массив.
	
```sql
-- ❌ NOT in 1NF (multiple values in one cell) 
orders order_id | customer | products
 1              | Alice    | "Laptop, Mouse, Keyboard" 
	 
 -- ✅ In 1NF (atomic values) 
orders 
order_id | customer | product 
1        | Alice    | Laptop
1        | Alice    | Mouse 
1        | Alice    | Keyboard
```
	
---
**2NF**
	
- выполняется 1NF
- нет частичной зависимости неключевых столбцов от части составного ключа.
	
```sql
-- ❌ NOT in 2NF (course_name depends only on course_id, not full key) 
enrollments 
 student_id | course_id | course_name | grade
 1          | 101       | Math        | A
 2          | 101       | Math        | B 
	 
 -- ✅ In 2NF (split into two tables) 
enrollments 
 student_id | course_id | grade
 1          | 101       | A    
 2          | 101       | B 
	 
courses 
course_id | course_name 
101       | Math
```
	
---
**3NF**
	
- выполняется 2NF
- Нет транзитивных зависимостей: неключевые атрибуты зависят только от ключа, а не друг от друга.
	
```sql
-- ❌ NOT in 3NF (dept_location depends on dept_id, not employee_id)
employees
employee_id | name  | dept_id | dept_location
1           | Alice | 10      | NY
2           | Bob   | 10      | NY
	
-- ✅ In 3NF (split department info)
employees
employee_id | name  | dept_id
1           | Alice | 10
2           | Bob   | 10
	
departments
dept_id | dept_location
10      | NY
```
	
---
**Преимущества**
- простота содержания и обновления данных
- экономия места на диске
- устраняет избыточные данные
- Быстрая запись данных
- Согласованность данных (данные хранятся только в одном месте, в одной таблице)
<!--ID: 1769280257992-->

