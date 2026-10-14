
04-10-2026 16:14

Status:

Tags: [[Python]]

---
# Comparisons - Python



### 1. `==` и `__eq__()`

При использовании:

```python
a == b
```

Python **неявно вызывает** специальный метод:

```python
a.__eq__(b)
```

Например:

```python
class User:
    def __init__(self, name):
        self.name = name

    def __eq__(self, other):
        return self.name == other.name
```

Теперь:

```python
u1 = User("John")
u2 = User("John")

u1 == u2       # True
```

Без переопределения `__eq__` объекты пользовательского класса обычно сравниваются по **идентичности**, унаследованной от `object`.

---

### 2. `==` vs `is`

Очень важно не путать:

```python
a == b
```

→ **равны ли значения?**

```python
a is b
```

→ **это один и тот же объект?**

Пример:

```python
a = [1, 2]
b = [1, 2]

a == b    # True
a is b    # False
```

`is` особенно используется для проверки `None`:

```python
if value is None:
    ...
```



---
### 3. Другие специальные методы

Операторы сравнения также соответствуют специальным методам:

```text
==  → __eq__()
!=  → __ne__()
<   → __lt__()
<=  → __le__()
>   → __gt__()
>=  → __ge__()
```

Например:

```python
a < b
```

в общем случае приводит к вызову:

```python
a.__lt__(b)
```



---
### 4. Chained comparisons

Python позволяет писать цепочки:

```python
18 <= age < 65
```

Это эквивалентно:

```python
18 <= age and age < 65
```

При этом `age` вычисляется только один раз.


---
### Коротко

- `==` → сравнение **значений**
    
- `is` → сравнение **идентичности объектов**
    
- `a == b` → `a.__eq__(b)`
    
- `!=` → `__ne__`, `<` → `__lt__`, `>` → `__gt__` и т.д.
    
- Для `None` используй `is None`, а не `== None`.


----
#### [[Comparisons - Python - Flashcards|Link to flashcards]]



---
### References:

