
27-06-2026 09:35

Status:

Tags: [[Mongo DB]] [[Базы данных]]

---
# Updating documents (JS shell) - Mongo DB



### `updateOne()`

```js
db.<collection>.updateOne(
	<filter>, //document
	<update>, //document
	{options} //object
)
```

- `<filter>` - содержит [[Querying (JS shell) - Mongo DB#Фильтрация|критерии фильтрации]]

- `<update>` - contains update operator expression that will be used in the update

- `{options}` - НЕ ОБЯЗАТЕЛЬНЫЙ аргумент.



#### Пример `updateOne()`

Нужно добавить новое поле `subscribers` : int - кол-во подписчиков.

1. проверяем, есть ли документ:

![[Pasted image 20260627094337.png]]


2.  Используем `$set` для  добавления обычного поля и установления его значения.
 ![[Pasted image 20260627094530.png]]

Получаем результат:

![[Pasted image 20260627094550.png|268]]

- `matchCount` - кол-во найденных документов по документу фильтрации

- `modifiedDocuments` - кол-во изменённых документов с применением update документа.




---
### `updateMany()`

```js
db.<collection>.updateMany(
	<filter>,
	<update>,
	{options}
)
```

> [!warning]
> Если `updateMany()` fails -> it will not roll back already applied updates.
>
> В результате только часть документов будет обновлена. Если это произойдет -> нужно просто еще раз использовать `updateMany()` для обновления оставшихся документов.


> [!example] Warning
> `updateMany()` **~={orange}lacks isolation.=~**
>
>Updates will be visible as soon they're performed.


#### Пример `updateMany()`


```js
db.books.updateMany(
	{ publishedDate: { $lt: ISODate("2019-01-01T00:00:00.000Z") } },
	{ $set: { status: "LEGACY" } }
	//no options
)
```

**Output:**

![[Pasted image 20260627104322.png|278]]


----
### Update operators


#### `$set`

1. Добавляет новые поля и значения в документ

или

2. Replaces the value of a field with a specified value


---
#### `$push`

1. Appends a value to an array

or

2. If absent, `$push` adds the aray field + записывает в него новые значения



---
### Update options


#### upsert option

Upsert - short for "Update or Insert".

Insert document with provided information if matching documents don't exist.

**Usage:**

![[Pasted image 20260627095035.png]]

**Output:**

![[Pasted image 20260627095052.png]]


----
#### [[Updating documents (JS shell) - Mongo DB - Flashcards|Link to flashcards]]



---
### References:

