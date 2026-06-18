
14-09-2025 18:36

Status: #child 

Tags: [[SQL]]

---
# Regular expressions - SQL

Позволяют проверять данные на соответствие более сложным паттернам чем позволяет оператор ~={cyan}LIKE=~.

## Синтаксис

```sql
... WHERE table_field ~ 'pattern';   -- case-sensitive
... WHERE table_field ~* 'pattern';  -- case-insensitive
-- 'pattern' - это собственно и есть reg ex
```

- `~` - ~={cyan}case-sensitive=~ сравнение
-  `~*` - ~={cyan}case-insensitive=~  сравнение


### Специальные символы и конструкции

> [!warning] **Поиск подстрок через regex**
> Чтобы найти подстроку нужно так её и написать в regexe:
>```sql
SELECT id, address WHERE address ~* 'Avenue';
>```
>А чтобы проверить совпадение со списком символов в любом порядке - используем скобки `[ ]`:
>```sql
SELECT * FROM  Subject WHERE name ~ '[ey]';
>```

| Characters and constructs | Matches                                                                               |     |
| :------------------------ | :------------------------------------------------------------------------------------ | --- |
| *                         | 0 или более символов соотвтетствующих регексу, после которого стоит этот символ (`*`) |     |
| +                         | 1 или более символов соотвтетствующих регексу, после которого стоит этот символ (`+`) |     |
| .                         | Любой символ                                                                          |     |
| ?                         | 0 или 1 соотвтетствующих регексу, после которого стоит этот символ (`?`)              |     |
| ^                         | Сравнение с началом строки                                                            |     |
| $                         | Сравнение с концом строки                                                             |     |
| [abc]                     | есть любой из символов, содержащихся внутри квадратных скобок                         |     |
| `[^abc]`                  | нет любого из символов, содержащихся внутри квадратных скобок                         |     |
| [A-Z]                     | Любая заглавная буква (A–Z)                                                           |     |
| [a-z]                     | Любая строчная буква (a–z)                                                            |     |
| [0-9]                     | Любая цифра (0–9)                                                                     |     |
| p1\|p2\|p3                | Любой из паттернов p1 или p2 или p3                                                   |     |
| {n}                       | n повторений подстроки, соответствующей предыдущему регексу                           |     |
| {m,n}                     | От m до n повторений подстроки, соответствующей предыдущему регексу                   |     |

> [!note] **Использование специальных символов**
> Если нужно использовать точку как часть подстроки паттерна, а не как специальный символ, обозначающий любой символ. => использем `\`:
> - `.` - любой символ
> -  `\.` - точка


## Примеры

- **Get all users whose names start with "John":**

```sql
SELECT * FROM Users WHERE name ~ '^John'
```
	
	
- **Display all school subjects whose names end with the letter "e" or "y":**

```sql
SELECT * FROM  Subject WHERE name ~ '[ey]$'
```
	
	
- **Find all users whose email addresses end with "@outlook.com" or "@icloud.com":**

```sql
SELECT * FROM Users WHERE email ~ '@(outlook|icloud)\.com$'
```
	
	
- **Find all users whose phone numbers do not contain the digits "2" and "8":**

```sql
SELECT * FROM Users WHERE phone_number ~ '^[^28]*$'
```
	 Про `*`
– Квантификатор: «0 или более повторений предыдущего выражения».  
– Здесь относится к `[^28]`.  
👉 То есть: «любое количество символов, не равных `'2'` или `'8'` (включая пустую строку)».
	
	
- **Find all users whose phone number starts with «+7»**

```sql
SELECT name, phone_number FROM Users WHERE phone_number ~ '^\+7'
```
	
	
- Find addresses that contain the string 'Avenue'
```sql
SELECT id, address WHERE address ~* 'Avenue';
```

----
#### [[Regular expressions - SQL - Flashcards|Link to flashcards]]



---
### References:

