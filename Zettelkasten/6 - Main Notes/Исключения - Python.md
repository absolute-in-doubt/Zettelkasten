
03-10-2026 18:06

Status:

Tags: [[Python]]

---
# Исключения - Python


```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Нельзя делить на ноль")
```

`try` — код, который может вызвать исключение.  
`except` — обработчик конкретного исключения.

---

### 2. Несколько исключений

```python
try:
    value = int(input())
    result = 10 / value
except ValueError:
    print("Не число")
except ZeroDivisionError:
    print("Ноль")
```

Можно обработать несколько типов одним `except`:

```python
except (ValueError, TypeError):
    ...
```

---

### 3. `else`

Выполняется **только если исключения не произошло**:

```python
try:
    value = int("123")
except ValueError:
    print("Ошибка")
else:
    print("Успешно:", value)
```

Удобно отделять основной успешный код от обработки ошибок.

---

### 4. `finally`

Выполняется **в любом случае**:

```python
try:
    file = open("data.txt")
    ...
except FileNotFoundError:
    print("Файл не найден")
finally:
    file.close()
```

Обычно используется для освобождения ресурсов.

На практике для файлов лучше использовать `with`:

```python
with open("data.txt") as file:
    data = file.read()
```

---

### 5. `raise` — выбросить исключение

```python
if age < 0:
    raise ValueError("Age cannot be negative")
```

Можно пробросить пойманное исключение дальше:

```python
try:
    ...
except ValueError:
    raise
```

---

### 6. Свои исключения

Исключение — обычный класс, наследующийся от `Exception`:

```python
class InvalidFileError(Exception):
    pass
```

Использование:

```python
raise InvalidFileError("Unsupported file format")
```

Обработка:

```python
try:
    ...
except InvalidFileError as e:
    print(e)
```

---

### 7. Иерархия исключений

Например:

```text
BaseException
└── Exception
    ├── ValueError
    ├── TypeError
    ├── OSError
    │   ├── FileNotFoundError
    │   └── PermissionError
    └── RuntimeError
```

Поэтому лучше ловить **конкретный тип**:

```python
except FileNotFoundError:
```

а не:

```python
except Exception:
```

если нет необходимости обработать любые обычные ошибки.

---

### 8. `as e`

Позволяет получить объект исключения:

```python
try:
    int("abc")
except ValueError as e:
    print(e)
```

`e` содержит сообщение и другую информацию об исключении.

---

### 9. Важный принцип

Не стоит использовать исключения для обычной логики:

```python
# плохо
try:
    value = my_dict["name"]
except KeyError:
    value = "Unknown"
```

Если проверка ожидаема:

```python
value = my_dict.get("name", "Unknown")
```

Но если ошибка действительно является исключительной ситуацией — `try/except` подходит.


----
#### [[Исключения - Python - Flashcards|Link to flashcards]]



---
### References:

