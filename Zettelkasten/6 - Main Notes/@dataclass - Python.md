
04-10-2026 16:18

Status:

Tags: [[Python]]

---
# `@dataclass` (Python 3.7+)

Автоматическая генерация `__init__`, `__repr__`, `__eq__`.

```python
from dataclasses import dataclass

@dataclass
class Product:
    name: str
    price: float
    stock: int = 0  # значение по умолчанию

    def total_value(self) -> float:
        return self.price * self.stock

p = Product("Laptop", 999.99, 5)
print(p)  # Product(name='Laptop', price=999.99, stock=5)
```


> [!note]
> Обычно dataclasses используются искючительно для задания data объектов без аттрибутов уровня класса. Но все же можно создать атрибут класса через `ClassVar:`

```python
from dataclasses import dataclass, field
from typing import ClassVar

@dataclass
class Point:
    x: float
    y: float
    origin: ClassVar[tuple[float, float]] = (0.0, 0.0)  # атрибут класса
    counter: ClassVar[int] = 0  # общий счётчик

p1 = Point(1.0, 2.0)
p2 = Point(3.0, 4.0)

print(Point.origin)  # (0.0, 0.0)
print(p1.origin)     # (0.0, 0.0) — доступно, но не поле экземпляра
```

`ClassVar` поля **не включаются** в `__init__`, `__repr__`, `__eq__`.





----
#### [[Data classes - Python - Flashcards|Link to flashcards]]



---
### References:

