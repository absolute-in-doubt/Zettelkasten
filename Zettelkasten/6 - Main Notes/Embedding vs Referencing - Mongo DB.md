
26-06-2026 12:26

Status:

Tags: [[Mongo DB]] [[Базы данных]]

---
# Embedding vs Referencing - Mongo DB

**Golden rule:**
	~={yellow}Data that is accessed together, should be stored together.=~


- embed data that is queried together;
- avoid unbounded arrays;
- one-to-few → embed;
- one-to-many/many-to-many → references;
- data that changes independently → references;
- data with independent lifecycle → references.


----
### Guidelines

Есть набор Guideline-ов. Проходимся по каждому и после этого даём совокупную оценку каждому подходу.

![[Pasted image 20260626122758.png|388]]


> [!note]
> Иногда, какой-то отдельный guideline может быть намного важнее всех остальных.



Рассмотрим пример "printed books" to "authors":

![[Pasted image 20260626122941.png|377]]


----
### 1. Simplicity

![[Pasted image 20260626122840.png]]

Да, хранение PrintedBooks и authors в одном документе упрощает код приложения:
**+1 point - Embedding**


---
### 2. Go Together

![[Pasted image 20260626123124.png]]

Да, "a book has an author":

**+1 point - Embedding**


---
### 3. Query Atomicity

![[Pasted image 20260626123246.png]]

Да, когда приложение получает книги, ему также нужны и авторы книг сразу же:
**+1 point - Embedding**


---
### 4. Update Complexity

![[Pasted image 20260626123539.png]]

Нет, мы можем поменять биографию автора без изменения книг, которые он писал:
**+1 point - Referencing**


---
### 5. Archival (Deletion)

![[Pasted image 20260626123658.png]]


Нет, у автора может быть больше, чем одна книга. Мы не хотим удалять автора, если одна из его книг удаляется:
**+1 point - Referencing**


---
### 6. Cardinality

![[Pasted image 20260626123832.png]]

"Есть ли риск того, что связанных сущностей будет бесконечно много (их список будет бесконечно расти)?" 

Нет, в нашем случае количество авторов не будет бесконечным:
**+1 point - Embedding**


> **~={red}?!=~** **In this case `No` - is actually a point for Embedding** 


---
### 7. Data Duplication

![[Pasted image 20260626124307.png]]

Нет, в случае эмбеддинга (дубликации авторов) их management не сильно сложный:
**+1 point - Embedding**


> **~={red}?!=~** **In this case `No` - is actually a point for Embedding**



---
### 8. Document Size

![[Pasted image 20260626124448.png]]

Нет, документ книги + несколько авторов будет относительно небольшим:
**+1 point - Embedding**


> **~={red}?!=~** **In this case `No` - is actually a point for Embedding**


---
### 9. Document Growth


![[Pasted image 20260626124700.png]]

Будет ли документ постоянно увеличиваться после создания?

Нет, почти не будет роста размера документа со временем:
**+1 point - Embedding**


> **~={red}?!=~** **In this case `No` - is actually a point for Embedding** 


---
### 10. Workload (Writing)

![[Pasted image 20260626130637.png]]


Нет, данные о новой книге и её авторе будут записаны в одной и той же операции. + У нас даже не write-heavy workload, поскольку добавлять новые книги будут редко:
**+1 point - Embedding**


> **~={red}?!=~** **In this case `No` - is actually a point for Embedding** 


---
### 11. Individuality

![[Pasted image 20260626131014.png]]

Нет, author не может существовать в нашем прилоджении без книг:
**+1 point - Embedding**


> **~={red}?!=~** **In this case `No` - is actually a point for Embedding** 


---
## Results:

![[Pasted image 20260626131149.png]]

**Embed Total: 9**
**Referencing Total: 2**

Используем Embedding.

----
#### [[Embedding vs Referencing - Mongo DB - Flashcards|Link to flashcards]]



---
### References:

[Relational (SQL) to Document Model | MongoDB University](https://learn.mongodb.com/learn/course/relational-to-document-model/relational-to-document-model/design-relationships?page=2)