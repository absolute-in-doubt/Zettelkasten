
12-10-2026 12:27

Status:

Tags: [[pandas - Python]] [[Python]]

---
# Filtering Data - Pandas

Базовое решение для фильтрации - [[Просмотр частей df - Pandas#`loc`- access via textual & numerical info|loc]], т.к. он позволяет отбирвать строки по предикату:

```python
df.loc[df['Type 1'] == "Grass"]

df.loc[(df['Type 1'] == "Grass") & (df["Type 2"] == "Poison")]

df.loc[(df['Type 1'] == "Grass") & (df["Type 2"] == "Poison") & (df["HP"] > 70)]
```

> [!note]
> Для составных условий в `iloc` обязательно каждое подусловие брать в скобки:
> ```python
> df.loc[(df['Type 1'] == "Grass") & (df["Type 2"] == "Poison")]
> ```
> Вот так арботать не будет:
> ```python
> df.loc[df['Type 1'] == "Grass" & df["Type 2"] == "Poison"]
> ```



### Pandas logical operators convention

Оно отличается от того, что принято в целом в Python
`|` - or
`&` - and

Но порядок тот же:
1. not
2. and
3. or


----
#### [[Filtering Data - Pandas - Flashcards|Link to flashcards]]



---
### References:

