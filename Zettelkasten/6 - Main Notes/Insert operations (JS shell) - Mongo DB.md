
27-06-2026 09:09

Status:

Tags: [[Mongo DB]] [[Базы данных]]

---
# Insert operations (JS shell)- Mongo DB


> [!example]
> Каждый документ в коллекции должен иметь `_id` поле и его значение должно быть уникально в этой коллекции.
>
>Если при создании объекта `insert` не передать конкретного значения `_id` - MongoDB сгенерирует его самостоятельно.


---

### `insertOne()`


**Usage:**

(вызывается на коллекции, которая в свою очередь вызывается на объекте БД):

```javascript
db.<collection>.insertOne()
```


> [!warning]
> Если вызвать метод:
> ```javascript
> db.my_collection.insertOne()
> ```
> Когда в БД нет коллекции `my_collection` - КОЛЛЕКЦИЯ БУДЕТ СОЗДАНА АВТОМАТИЧЕСКИ.


**Пример полной команды:**

![[Pasted image 20260627091442.png]]


**Результат:**

![[Pasted image 20260627091503.png]]


---
### `insertMany()`

Вставка multiple comma-separated documents: 

![[Pasted image 20260627091913.png]]
![[Pasted image 20260627092037.png]]


**Output:**

![[Pasted image 20260627092101.png]]

----
#### [[Insert operations (JS shell)- Mongo DB - Flashcards|Link to flashcards]]



---
### References:

