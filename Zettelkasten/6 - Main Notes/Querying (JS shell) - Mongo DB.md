
27-06-2026 09:23

Status:

Tags: [[Mongo DB]] [[Базы данных]]

---
# Querying (JS shell) - Mongo DB


### `findOne()`

To find a single document matching a condition, use the `findOne()` command with the `$eq` operator.

Here's an example:

```js
db.books.findOne({title: {$eq: "Brave New World"}})
```


---
### `find()`


**Usage:**

```js
db.<collection>.find()
```

**~={red}?!=~** Если запустить `db.my_collection.find()` без параметров - выведет все документы этой коллекции.



### Фильтрация

```js
<field>: { <operator>: <value> }
```

#### `$eq`

```js
//Explicit:
{ field: { $eq: <value> } }

//Implicit:
{ field: { <value> } }
```

**Usage:**

```js
db.zips.find({ state: "AZ" })  //Найдет все адреса штата Arizona
```



---
#### `$in`

Оператор `$in` позволяет выбирать все документы, имеющие значение указанного поля равное  одному из значений in the specified array.

![[Pasted image 20260627093125.png|350]]

**Usage:**

```js
db.zips.find({ city: { $in: ["PHOENIX", "CHICAGO"] } })
```


---
#### `$lt`

`$lt` - less than

**Usage:**

```js
db.books.updateMany(
	{ publishedDate: { $lt: ISODate("2019-01-01T00:00:00.000Z") } },
	{ $set: { status: "LEGACY" } }
	//no options
)
```


#### `$lte` 

`$lte` - less than or equal to


---
#### `$gt`

`$gt` greater than

![[Pasted image 20260627110705.png]]

~={orange}**Фильтруем по вложенному документу**=~

```js
db.sales.find({ "items.price": { $gt: 50} })
```


#### `$gte`

`$gte` greater than or equal to


---



#### Logical operators


#### `$and`

```js
//Explicit syntax:
db.<collection>.find({
	$and: [
		{<expression>},
		{<expression>}
	]
})

//Implicit syntax:
db.<collection>.find( { <expression>, <expression> } )
```


---
#### `$or`

```js
//ONLY EXPLICIT FORM:

db.<collection>.find({
	$or: [
		{<expression>},
		{<expression>}
	]
})
```


#### Пример комбинирования `$and` и `$or`

> [!warning]
> При комбинировании нельзя использовать implicit `$and` syntax: 
> 
>В этом случае второе условие `$or` переопределяет первое, поскольку в filter document может быть только одно поле `$or`.


```js
db.routes.find({
	$and: [
		{ $or: [
			{ cdst_airport: "SEA"},
			{ src_airport: "SEA"}
			]},
		{ $or: [
			{ airline: "American Airlines" },
			{ airplane: 320 }
			]},
		}
	]
})
```



----
#### [[Querying (JS shell) - Mongo DB - Flashcards|Link to flashcards]]



---
### References:

