
27-06-2026 10:50

Status:

Tags: [[Mongo DB]] [[Базы данных]]

---
# Deleting documents (JS shell) - Mongo DB



### `deleteOne()`

```js
db.<collection>.deleteOne(
	<filter>, //document
	{options} //object
)
```

- `{options}` - НЕ ОБЯЗАТЕЛЬНЫЙ АРГУМЕНТ


**Usage:**

```js
db.podcasts.deleteOne({_id: ObjectId("6303ed190b5dff15e3ac7ae6")})
```

**Output:**

```js
{ 
	acknowledged: true, 
	deletedCount: 1 
}
```


---
### `deleteMany()`


```js
db.<collection>.deleteMany(
	<filter>,
	{options}
)
```

- `{options}` - НЕ ОБЯЗАТЕЛЬНЫЙ АРГУМЕНТ


**Usage:**

```js
db.podcasts.deleteMany( {category: "crime"} )
```

**Output:**

```js
{
	acknowledged: true,
	deletedCount: 3
}
```


----
#### [[Deleting documents (JS shell) - Mongo DB - Flashcards|Link to flashcards]]



---
### References:

