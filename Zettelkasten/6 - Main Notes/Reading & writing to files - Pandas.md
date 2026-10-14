
12-10-2026 11:37

Status:

Tags: [[pandas - Python]] [[Python]]

---
# Reading & writing to files - Pandas


### Reading

```python
import pandas as pd

df = pd.read_csv('pokemon_data.csv')

df_xlsx = pd.read_excel("./pandas/pokemon_data.xlsx")

df_txt = pd.read_csv("./pandas/pokemon_data.txt", delimiter='\t') # tab sepearted values from a txt file
```


### Writing

```python
df.to_csv("./pandas/modified.csv") # сохранит вместе с индексом

df.to_csv("./pandas/mmodified.csv", index=False)  

df.to_excel("./pandas/modified.xlsx", index=False)

# To tab separated csv in a txt file:
df.to_csv("./pandas/modified.txt", index=False, sep='\t')
# Тут sep (separator) это то же самое, что и delimiter при read
```

----
#### [[Reading & writing to files - Pandas - Flashcards|Link to flashcards]]



---
### References:

