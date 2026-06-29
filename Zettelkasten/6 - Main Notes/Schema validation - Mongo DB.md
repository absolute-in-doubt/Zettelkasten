
26-06-2026 15:11

Status:

Tags: [[Mongo DB]] [[Базы данных]]

---
# Schema validation - Mongo DB


Документы с различными схемами могут сосуществовать в одной коллекции в MongDB:

![[Pasted image 20260626151309.png]]


### Validation Rules

Специальные правила типа "`username` обязательно должен быть `String`".

Validation Rules определяются при создании коллекции. 
**~={orange}Validation Rules применяется ко всем документам в коллекции.=~**


#### Level of enforcement


**~={red}?!=~** Если добавить Validation Rule к существующей коллекции, только newly inserted values будут проверяться. Существующие документы будут проверяться только в случае запроса на их обновление (и то, проверяться будет новое значение).

![[Pasted image 20260626152318.png]]


**~={red}?!=~** Invalid existing documents не будут проверяться ни при каком enforcement level.
Но их можно найти при помощи:
```
db.collection.find( { $nor: [ validator ] } )
```


---
### Способы задания Validation Rules:


**Query Operators**



**JSON Schema** - самый популярный способ

Чтобы создать Json Schema - используем `$jsonSchema` оператор.



### Пример задания Validation Rules

![[Pasted image 20260626152032.png]]


`$lt` - query operator - less than

`$jsonSchema` - задаёт тип для значений


---
### Validation Action

Validation Action - то, что происходит с документом after it fails the validation

Two options:

![[Pasted image 20260626152652.png]]



----
### Полный пример валидации

Для Review document в Book Store App нужно, чтобы рисутствовали следующие поля с корректными типамми:

- `review_id`
- `user_id`
- `timestamp`
- `review`
- `rating`
- `comments`

#### Json Schema 

(value of the `$jsonSchema` operator)

![[Pasted image 20260626152910.png]]


![[Pasted image 20260626153130.png]]

P.S. это JavaScript

----
#### [[Schema validation - Mongo DB - Flashcards|Link to flashcards]]



---
### References:

