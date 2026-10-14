
11-10-2026 18:25

Status:

Tags: [[pandas - Python]] [[Python]]

---
# Просмотр частей df - Pandas




```python
print(df.head()) # prints first 5 rows

print(df.head(20)) # prints first 20 rows

print(df.tail(20)) # prints 20 bottom rows

```


---
### Read headers

```python
print(df.columns)
```

```
Index(['#', 'Name', 'Type 1', 'Type 2', 'HP', 'Attack', 'Defense', 'Sp. Atk',
       'Sp. Def', 'Speed', 'Generation', 'Legendary'],
      dtype='object')
```


### Read a specific column's values

```python
print(df['Name'])

print(df.Name) # тоже самое, но с навзаниями содержащими несколько слов - не варик
```

```
0                  Bulbasaur
1                    Ivysaur
2                   Venusaur
3      VenusaurMega Venusaur
4                 Charmander
               ...          
795                  Diancie
796      DiancieMega Diancie
797      HoopaHoopa Confined
798       HoopaHoopa Unbound
799                Volcanion
Name: Name, Length: 800, dtype: object
```

```python
print(df['Name'][0:5]) # выведет только от нуля до 5 индекса 
```

```
0                Bulbasaur
1                  Ivysaur
2                 Venusaur
3    VenusaurMega Venusaur
4               Charmander
Name: Name, dtype: object
```

```python
print(df[['Name', 'Type 1', 'HP']]) # columns list - КАК ОДИН АРГУМЕНТ - list
```



---
### Df rows random access

#### `iloc` - integer location

```python
print(df.iloc[1])
```

```
#                   2
Name          Ivysaur
Type 1          Grass
Type 2         Poison
HP                 60
Attack             62
Defense            63
Sp. Atk            80
Sp. Def            80
Speed              60
Generation          1
Legendary       False
Name: 1, dtype: object
```


```python
print(df.iloc[1:4])
```

```
   #                   Name Type 1  Type 2  HP  Attack  Defense  Sp. Atk  \
1  2                Ivysaur  Grass  Poison  60      62       63       80   
2  3               Venusaur  Grass  Poison  80      82       83      100   
3  3  VenusaurMega Venusaur  Grass  Poison  80     100      123      122   

   Sp. Def  Speed  Generation  Legendary  
1       80     60           1      False  
2      100     80           1      False  
3      120     80           1      False
```

Output of a specific row and specific column:

```python
row_idx = 2
col_idx = 1

print(df.iloc[row_idx,col_idx]) # Venusaur


# Просмотр всех строк с колонками с 4 индекса до 10 НЕ включительно
df.iloc[:,4:10]

```



#### `loc`- access via textual & numerical info

По сути - фильтрация по предикату

```python
fire_typed = df.loc[df['Type 1'] == "Fire"]

print(fire_typed)
```


---
### Итерация по df

```python
for index, row in df.iterrows():
    print(f"{index} - {row['Name']}")
```


----
#### [[Просмотр частей df - Pandas - Flashcards|Link to flashcards]]



---
### References:

