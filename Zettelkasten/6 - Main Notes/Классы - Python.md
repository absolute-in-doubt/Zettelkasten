
03-10-2026 17:25

Status:

Tags: [[Python]]

---
# Классы - Python

Импорт других файлов:

```
project/
├── utils/
│   ├── __init__.py
│   ├── file_utils.py
│   ├── csv_utils.py
│   └── json_utils.py
└── main.py
```

В `csv_utils.py`:

```python
from .file_utils import FileUtils
```


---
## 1. Классы и объекты

**Класс** — шаблон для создания объектов.  
**Объект** — экземпляр класса.

```python
class Person:
    def __init__(self, name: str, age: int):
        self.name = name  # атрибут экземпляра
        self.age = age

    def greet(self):
        return f"Hello, I'm {self.name}"

p = Person("Alice", 30)  # создание объекта
print(p.greet())
```

---

## 2. `__init__` и `self`

- `__init__` — конструктор, вызывается при создании объекта.
- `self` — ссылка на текущий экземпляр (аналог `this` в Java).

```python
class Point:
    def __init__(self, x: float, y: float):
        self.x = x
        self.y = y
```

---

## 3. Атрибуты класса и экземпляра

```python
class Counter:
    total = 0  # атрибут класса (общий для всех)

    def __init__(self):
        self.count = 0  # атрибут экземпляра

    def increment(self):
        self.count += 1
        Counter.total += 1
```

> [!warning]
> Атрибуты экземпляров не прописываются в самом классе, а только в методах. Это не особо удобно. Можно объявлять их как пустышки для большей наглядности:
> ```python
> class Person:
>    name: str      # аннотация типа, атрибут экземпляра
>    age: int       # только подсказка, не создаёт поле
>
>    def __init__(self, name: str, age: int):
>        self.name = name
>        self.age = age
> ```
>
>Или использовать [[Классы - Python#9. `@dataclass` (Python 3.7+)|dataclasses]] - в них все поля являются атрибутами экземпляра.


---
### 3.1 Static-методы

В Python **есть** static методы. Вы можете объявить метод, который не получает ни `self` (экземпляр), ни `cls` (класс).

```python
class DateUtils:
    @staticmethod
    def is_leap_year(year: int) -> bool:
        return (year % 4 == 0 and year % 100 != 0) or (year % 400 == 0)

    @classmethod
    def from_timestamp(cls, ts: int):
        # может создать экземпляр класса
        return cls()

DateUtils.is_leap_year(2024)  # True
```

**~={orange}Такой метод не знает ни об экземпляре, ни о классе. Это просто функция, логически принадлежащая классу.=~**

|Тип метода|Декоратор|Первый аргумент|Доступ к экземпляру|Доступ к классу|
|---|---|---|---|---|
|Обычный метод|—|`self`|✅|❌ (только через `self.__class__`)|
|Class method|`@classmethod`|`cls`|❌|✅|
|Static method|`@staticmethod`|нет|❌|❌|

---

## 4. Инкапсуляция

Ограничение доступа к внутренним данным.

- `_attr` — условно защищённый (protected)
- `__attr` — приватный (name mangling)

```python
class BankAccount:
    def __init__(self, balance: float):
        self.__balance = balance  # приватный атрибут

    def deposit(self, amount: float):
        if amount > 0:
            self.__balance += amount

    def get_balance(self) -> float:
        return self.__balance
```

---

## 5. `@property` — геттеры и сеттеры

```python
class Temperature:
    def __init__(self, celsius: float):
        self._celsius = celsius

    @property
    def celsius(self) -> float:
        return self._celsius

    @celsius.setter
    def celsius(self, value: float):
        if value < -273.15:
            raise ValueError("Too low")
        self._celsius = value
```

---

## 6. Наследование

```python
class Animal:
    def speak(self):
        return "..."

class Dog(Animal):  # Dog наследует Animal
    def speak(self):  # переопределение метода
        return "Woof!"
```

**Вызов метода родителя:**

```python
class Cat(Animal):
    def speak(self):
        return super().speak() + " Meow!"
```

---

## 7. Полиморфизм

Одинаковый интерфейс для разных классов.

```python
def make_sound(animal: Animal):
    print(animal.speak())

make_sound(Dog())  # Woof!
make_sound(Cat())  # ... Meow!
```

---

## 8. Магические методы (dunder)

- `__str__` — человекочитаемое представление (`str(obj)`)
- `__repr__` — отладочное представление (`repr(obj)`)
- `__eq__` — сравнение на равенство (`==`)
- `__len__` — длина (`len(obj)`)
- `__add__` — сложение (`+`)

```python
class Vector:
    def __init__(self, x: int, y: int):
        self.x = x
        self.y = y

    def __add__(self, other: "Vector") -> "Vector":
        return Vector(self.x + other.x, self.y + other.y)

    def __repr__(self) -> str:
        return f"Vector({self.x}, {self.y})"
```

---

## 9. Статические и классовые методы

```python
class MathUtils:
    @staticmethod
    def add(x: int, y: int) -> int:
        return x + y

    @classmethod
    def from_string(cls, s: str) -> "MathUtils":
        x, y = map(int, s.split(","))
        return cls()
```

- `@staticmethod` — не получает ни `self`, ни `cls`
- `@classmethod` — получает класс (`cls`) - **~={orange}static=~**


---

## 10. Множественное наследование

```python
class A:
    pass

class B:
    pass

class C(A, B):  # наследует оба
    pass
```

Порядок поиска методов — MRO (можно посмотреть через `C.__mro__`).


----
#### [[Классы - Python - Flashcards|Link to flashcards]]



---
### References:

