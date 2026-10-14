
04-10-2026 16:31

Status:

Tags: [[Python]]

---
# Type checking & Type comparing - Python




### 1. `isinstance()` — обычно предпочтительный вариант

```python
def process(value):
    if isinstance(value, str):
        print("String")
    elif isinstance(value, int):
        print("Integer")
    elif isinstance(value, list):
        print("List")
```

```python
process("hello")  # String
process(42)       # Integer
process([1, 2])   # List
```

`isinstance()` также учитывает наследование:

```python
class Animal:
    pass

class Dog(Animal):
    pass

dog = Dog()

isinstance(dog, Animal)  # True
```

Поэтому для проверки типа объекта обычно используют именно **`isinstance()`**.


---
### 2. `type()` — получить точный тип

```python
value = 42

print(type(value))
# <class 'int'>
```

Можно сравнивать:

```python
if type(value) is int:
    ...
```

Но это отличается от `isinstance()`:

```python
class MyInt(int):
    pass

value = MyInt(10)

type(value) is int              # False
isinstance(value, int)          # True
```

`type(x) is T` проверяет: **объект именно этого типа**.

`isinstance(x, T)` проверяет: **объект этого типа или его наследник**.



---
### 3. Несколько типов

```python
if isinstance(value, (int, float)):
    print("Number")
```



---
### 4. Разное поведение в зависимости от типа полученного аргумента

Для простого кода нормально:

```python
def save(value):
    if isinstance(value, str):
        save_string(value)
    elif isinstance(value, bytes):
        save_bytes(value)
    else:
        raise TypeError(f"Unsupported type: {type(value)}")
```

Но если у тебя много типов и классов, часто лучше использовать **полиморфизм**, а не огромный `if/elif`:

```python
class File:
    def save(self):
        ...


class CsvFile(File):
    def save(self):
        ...


class JsonFile(File):
    def save(self):
        ...
```

Тогда вызывающий код не должен знать конкретный тип:

```python
file.save()
```

**Для конспекта:**  
`type(obj)` → узнать точный тип; `isinstance(obj, Type)` → проверить тип с учётом наследования. Для type checking в большинстве случаев предпочитай `isinstance()`.

----
#### [[Type checking & Type comparing - Python - Flashcards|Link to flashcards]]



---
### References:

