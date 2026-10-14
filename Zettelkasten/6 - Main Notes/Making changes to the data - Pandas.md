
12-10-2026 11:13

Status:

Tags: [[pandas - Python]] [[Python]]

---
# Making changes to the data - Pandas



### Creating a column

1. Можно просто вызвать эту колонку и она автоматически создастся

```python
df["Total"]  = df["HP"] + df["Attack"] + df ["Defense"] + df["Speed"]
```


2.  Тоже присвоение, но сумма через [[Просмотр частей df - Pandas#`iloc` - integer location|iloc]]

```python
df["Total"] = df.iloc[:,4:10].sum(axis=1)

# axis = 1 - суммирует горизонтально
# axis = 0 - суммирует вертикально

```



---
### Deleting colums

```python
df = df.drop(columns=["Total"])
```

> [!note] 
> `df.drop()` - не удаляет колонку в исходном dataframe, а создает новую


---
### Reordering columns

```python
df = df[["Total", "HP", "Name"]]
```

Вставка диапазонами старых колонок:

```python
cols = list(df.columns.values)
df = df[cols[0:4] + [cols[-1]] + cols[4:12]]

cols[0:4] # '#', 'Name', 'Type 1', 'Type 2'
cols[-1] # 'Total' - reverse index (с конца списка)

[cols[-1]] # оборачиваем в list, т.к. конкатенируем листы
```

```
Список исхоодных колонок (df.columns):
Index(['#', 'Name', 'Type 1', 'Type 2', 'HP', 'Attack', 'Defense', 'Sp. Atk','Sp. Def', 'Speed', 'Generation', 'Legendary'], dtype='object')
```

----
#### [[Making changes to the data - Pandas - Flashcards|Link to flashcards]]



---
### References:

