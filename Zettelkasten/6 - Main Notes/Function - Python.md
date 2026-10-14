
28-08-2026 19:35

Status:

Tags: [[Python]]

---
# Function - Python

Функции в Python - являются объектами, то есть их можно возвращать и передавать в качестве аргументов.

- Функция в классе — отдельный объект (function).
    
- При вызове через экземпляр она **автоматически** получает `self` (становится bound method).
    
- Вы **не передаёте `self` вручную** при `p.greet()` — это делает интерпретатор.


#### 1. Функция в теле класса

Внутри класса `greet` — это **обычная функция**, хранящаяся в атрибуте класса:

```python
Person.greet  # <function Person.greet at 0x...>
```

Она **не связана** с экземпляром.


#### 2. Доступ через экземпляр

При вызове `p.greet()` Python автоматически передаёт `p` как первый аргумент (`self`):

```python
p = Person("Alice")
p.greet()  # эквивалентно Person.greet(p)
```

Здесь `p.greet` — это **bound method** (привязанный метод), который уже "помнит" экземпляр.


#### 3. Можно вызвать явно через класс

```python
Person.greet(p)  # то же самое, что p.greet()
```

Функция та же, но `self` передаётся вручную.


#### Дескриптор функции

Механизм работает через **протокол дескриптора**: функция реализует `__get__`, который при доступе `obj.func` создаёт bound method.

```python
type(Person.greet)   # <class 'function'>
type(p.greet)        # <class 'method'>
```


---
##### Возврат нескольких значений

В python можно возвращать несколько аргументтов, что по сути представляет собой возврат tuple:

```python
def math_shit(x, y):  
    return x+y, x*y  
  
results = math_shit(1,2)  
print(results) # (1,2)


# Но rule of thumb:
r1, r2 = math_shit(1,2)
```


#### Optional parameter


```python
def func(x, y, z=None):
	print(x, y, z)

func(1,2,3) 
func(1,2)

```


---
### Return value

в Python **не обязательно** указывать возвращаемый тип метода. Это **опциональная аннотация типов** (type hint), которая нужна только для:

- статических анализаторов (mypy, pyright),
    
- лучшей подсказки в IDE,
    
- документации кода.

```python
# Без аннотации (полностью валидно)
def add(x, y):
    return x + y

# С аннотацией возвращаемого типа
def add(x: int, y: int) -> int:
    return x + y
```

Еще примеры с более сложными типами:
```python
def greet(name: str) -> str:
    return f"Hello, {name}"

def get_items() -> list[str]:
    return ["a", "b"]

def process(data: dict[str, int]) -> None:
    print(data)  # ничего не возвращает
```

`None` — явное указание, что метод ничего не возвращает.

----
#### [[Function - Python - Flashcards|Link to flashcards]]



---
### References:

