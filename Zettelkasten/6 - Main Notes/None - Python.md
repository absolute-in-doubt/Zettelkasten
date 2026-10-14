
04-10-2026 16:09

Status:

Tags: [[Python]] 

---
# None - Python


### `None` — аналог `null`

В Python вместо `null` используется специальный объект **`None`**:

```python
value = None
```

Обычно означает: **«значения нет» / «значение отсутствует»**.

Например:

```python
def find_user(id):
    ...
    return None  # пользователь не найден
```



---
### Проверка на `None`

Используй `is` / `is not`:

```python
if value is None:
    print("Value is absent")
```

```python
if value is not None:
    print(value)
```

❌ Не рекомендуется:

```python
if value == None:
```

Причина — `None` является **единственным объектом (singleton)**, поэтому для проверки его идентичности используется `is`.

 

---
### `None` ≠ `False` ≠ `0` ≠ `""`

Они все являются _falsy_ значениями, но это разные объекты и значения:

```python
None
False
0
""
[]
```

Например:

```python
if not value:
    ...
```

проверяет, является ли значение _falsy_, а не конкретно `None`.

Если нужно проверить именно отсутствие значения:

```python
if value is None:
    ...
```




---
### Null-safety

В Python **нет встроенной null-safety**, аналогичной Kotlin:

```kotlin
val name: String?
```

или Java:

```java
Optional<String>
```

Переменная может в любой момент содержать `None`, если это допускается логикой программы:

```python
name = None

print(name.upper())
```

Получим:

```text
AttributeError: 'NoneType' object has no attribute 'upper'
```

Поэтому разработчик сам должен проверять `None` там, где он возможен:

```python
if name is not None:
    print(name.upper())
```




---
### Type hints для nullable-значений

Можно явно указать, что функция может вернуть `None`:

```python
def find_user(id: int) -> User | None:
    ...
```

То есть:

```text
User | None
   ↑
может быть User или None
```

В старом синтаксисе:

```python
from typing import Optional

def find_user(id: int) -> Optional[User]:
    ...
```

`Optional[User]` фактически означает то же самое, что `User | None`.



---
### Коротко

|Язык|Отсутствующее значение|
|---|---|
|Python|`None`|
|Java|`null`|
|Kotlin|`null`|
|JavaScript|`null` / `undefined`|

**Главное:** Python не обеспечивает compile-time null-safety. Используй `None` для отсутствующего значения, проверяй его через `is None` / `is not None`, а через type hints (`T | None`) явно показывай, что значение может отсутствовать.

----
#### [[None - Python - Flashcards|Link to flashcards]]



---
### References:

