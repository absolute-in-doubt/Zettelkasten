
26-06-2026 13:28

Status:

Tags: [[Mongo DB]] [[Базы данных]]

---
# One To Many - Mongo DB


### Embedded one-to-many

> [!warning]
> Желательно использовать именно Embedding.
> 
> Используем Referencing только в случае, если child objects array is unbound или может бесконечно расти

##### Via child objects array

![[Pasted image 20260626132847.png|478]]


#### Via key-Value child objectsarray

![[Pasted image 20260626133008.png]]


Обычно в качестве ключей используются id child объектов.


---
### Referencing


#### Parent side referencing

![[Pasted image 20260626133507.png]]

---
#### Child side referencing

![[Pasted image 20260626133430.png]]

---
#### Bidirectional referencing

![[Pasted image 20260626133604.png]]


----
#### [[One To Many - Mongo DB - Flashcards|Link to flashcards]]



---
### References:

