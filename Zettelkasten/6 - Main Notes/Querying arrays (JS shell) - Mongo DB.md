
27-06-2026 11:30

Status:

Tags: [[Mongo DB]] [[Базы данных]]

---
# Querying arrays (JS shell) - Mongo DB



### `elemMatch`

```js
db.<collection>.find({
	<field>: { 
		$elemMatch { $eq: <value> }
	}
})
```



Есть коллекция accounts:
```js
{
	"account_id": 452452,
	"limit": 10000,
	"products": [
		"CurrencyService",
		"Commodity",
		"InvestmentStock"
	]
}
```


### Пример без `elemMatch`

**~={orange}Поиск по вложенному полю:=~**

```js
db.accounts.find({ products: "InvestmentStock"})
```

Он вернёт документы с таким значением поля и с массивом, содержащим такое значение:

![[Pasted image 20260627113454.png]]


### Пример с `elemMatch`


```js
db.account.find({
	products: {
		$elemMatch: { $eq: "InvestmentStcok" }
	}
})
```

Такой запрос вернёт только те документы, у которых поле `products` является массивом и этот массив содержит "InvestmentStock" value.



### Пример: комбинированный запрос с `elemMatch`

Implicitly using `$and`:

```js
db.sales.find({
	items: {
		$elemMatch: { 
			name: "laptop", 
			price: { $gt: 800 }, 
			quantity: { $gte: 1 }
		},
	},
})
```

----
#### [[Querying arrays (JS shell) - Mongo DB - Flashcards|Link to flashcards]]



---
### References:

