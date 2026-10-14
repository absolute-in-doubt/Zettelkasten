
04-10-2026 16:07

Status:

Tags:

---
# pass - Python

`pass` — это **пустая инструкция**, которая ничего не делает. Используется там, где Python требует блок кода, но выполнять пока нечего.

```python
class FileUtils:
    pass
```

Или:

```python
if condition:
    pass
```

Или часто при заготовке метода:

```python
def read(self, filename):
    pass
```

---

Важно: `pass` **не означает `return`** и не завершает функцию — он просто ничего не делает.

```python
def test():
    pass

print(test())  # None
```

**Запомнить:** `pass` = _«здесь синтаксически нужен код, но делать ничего не нужно»_.

----
#### [[pass - Python - Flashcards|Link to flashcards]]



---
### References:

