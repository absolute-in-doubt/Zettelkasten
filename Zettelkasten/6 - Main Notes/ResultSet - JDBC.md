
06-10-2025 12:49

Status: #not_even_born 

Tags: [[Java+]] [[JDBC]]

---
# ResultSet - JDBC


~={cyan}Default ResultSet is not updatable.=~
+
~={cyan}Has a cursor that moves forward only=~

### Перемещение по ResultSet

~={red}!!!=~ Начальное положение - ~={yellow}перед первым элементом=~ ~={red}!!!=~

- boolean `next()` - перемещает на следующую строку, если больше строк нет - ~={cyan}returns false=~
- boolean `previous()` - перемещает предыдущую строку, если больше строк нет - ~={cyan}returns false=~
- boolean `first()` - moves to the first row of this `ResultSet` object.
- boolean `last()`
- boolean `beforeLast()`
- boolean `afterLast()`

- boolean `absolute(int rowNum)` - Moves the cursor to the given row number in this `ResultSet` object.
- boolean `relative(int rowNum)` - Двигает вперюд или назад (если `rowNum < 0`) на определённое число строк

## Извлечение содержимого строк

```java
ResultSet rs = statement.executeQuery("SELECT * FROM books");

while(rs.next()){
	String isbn = rs.getString(1); //1 - номер столбца
	Double price = rs.getDouble("Price"); // "Price" - имя столбца
	//можно и имя столбца и номер
}
```

### Типы прокрутки (scroll)

Есть три основных типа:

- `TYPE_FORWARD_ONLY` — ~={orange}(по-умолчанию)=~ можно только идти вперёд, `next()`; это самый лёгкий и быстрый вариант.​
    
- `TYPE_SCROLL_INSENSITIVE` — можно двигаться вперёд/назад (`next`, `previous`, `absolute`, `relative`), но изменения в БД после выборки не видны.​
    
- `TYPE_SCROLL_SENSITIVE` — тоже произвольная навигация, но изменения в БД могут становиться видимыми (в зависимости от драйвера и БД).



### ResultSetConcurrency

- CONCUR_UPDATABLE – позволяет вносить изменения в данные;
    
- CONCUR_READ_ONLY – параметр ~={orange}по умолчанию=~, стандартный ResultSet.


```java
Statement st = conn.createStatement(
                ResultSet.TYPE_SCROLL_SENSITIVE, ResultSet.CONCUR_UPDATABLE);
        ResultSet uprs = st.executeQuery(
            "SELECT * FROM " + dbName + ".COFFEES");

        while (uprs.next()) {
            float f = uprs.getFloat("PRICE");
            uprs.updateFloat( "PRICE", f * percentage);
            uprs.updateRow();
        }
```


----
#### [[ResultSet - JDBC - Flashcards|Link to flashcards]]



---
### References:

- [[JDBC setup]]