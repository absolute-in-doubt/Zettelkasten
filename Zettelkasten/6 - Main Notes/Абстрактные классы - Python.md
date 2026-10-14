
04-10-2026 16:17

Status:

Tags: [[Python]]

---
# Абстрактные классы - Python



```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self) -> float:
        pass

class Circle(Shape):
    def __init__(self, radius: float):
        self.radius = radius

    def area(self) -> float:
        return 3.14 * self.radius ** 2
```

Нельзя создать экземпляр `Shape`, только наследников.

> [!tip] Note
> Наследник **обязан реализовать** абстрактный метод, если наследник - конкретный класс.
> 
> Если не переопределить метод в наследнике:
> ```python
> class Circle(Shape):
> 	pass
> ```
> То получим:
> ```
> TypeError:
>Can't instantiate abstract class Cat
>with abstract method make_sound
> ```

----
#### [[Абстрактные классы - Python - Flashcards|Link to flashcards]]



---
### References:

- [[Классы - Python]]