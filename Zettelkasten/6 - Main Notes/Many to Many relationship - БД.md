
26-10-2025 20:54

Status: #child

Tags: [[Базы данных]]

---
# Many to Many relationship - БД

![[Pasted image 20251026212603.png]]


![[Pasted image 20250915204122.png]]


Чтобы упростить запросы используется связывающая таблица, которая превращет отношение в два One To Many отношения:

![[Pasted image 20250915213759.png]]

![[Pasted image 20251026212838.png]]

> [!warning] 
> В связывающей таблице две колонки формируют составной PRIMARY KEY, чтобы, например фильм 2 не относился к одному и тому же актёру несколько раз -> (2,2) - не должны повторяться.

Пример:
```sql
CREATE TABLE Students (
    student_id SERIAL PRIMARY KEY, 
    name TEXT NOT NULL,
    class INT NOT NULL
);

CREATE TABLE Activities (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    number_of_students INT DEFAULT 0
);

-- связывающая таблица
CREATE TABLE Student_Activities (
    student_id INT REFERENCES Students(student_id),
    course_id INT REFERENCES Activities(id), 
    PRIMARY KEY (student_id, course_id)
);
```
🔑 Таблица `StudentActivities` — это «связка», которая реализует отношение многие-ко-многим.


----
#### [[Many to Many relationship - БД - Flashcards|Link to flashcards]]



---
### References:

- [[DB Relationships]]
- [[One to Many relationship - БД]]
- [[One to One relationship - БД]]