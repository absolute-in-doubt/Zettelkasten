
12-10-2026 11:07

Status:

Tags: [[pandas - Python]] [[Python]]

---
# Sorting & Describing data - Pandas


Просмотр с сортировкой по колонке:

```python
df.sort_values("Name") # Сортирует по алфавиту по колонке Name

df.sort_values("Name",ascending=False) # по алфавиту от Z до A
```


Сортировка по двум колонкам:

```python
df.sort_values(["Name", "HP"],ascending=False) # по обеим по убыванию

df.sort_values(["Name", "HP"],ascending=[1,0]) # Name - по возрастанию (A-Z), HP - по убыванию
```

---
### Get df stats

```python
df.describe()
```



----
#### [[Sorting & Describing data - Pandas - Flashcards|Link to flashcards]]



---
### References:

