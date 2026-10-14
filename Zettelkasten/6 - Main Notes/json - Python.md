
03-10-2026 12:17

Status:

Tags: [[Python]]

---
# json - Python

```python
import json
x = [1, 'simple', 'list']
json.dumps(x)


from io import StringIO
io = StringIO()
json.dump(['streaming API'], io)
io.getvalue()
```

##### Decoding JSON

```python
import json
json.loads('["foo", {"bar":["baz", null, 1.0, 2]}]')

json.loads('"\\"foo\\bar"')

from io import StringIO
io = StringIO('["streaming API"]')
json.load(io)
```


---
#### Запись json в файл

```python
json.dump(x, f)
```

### Чтение json из файла

```python
x = json.load(f)
```


----
#### [[json - Python - Flashcards|Link to flashcards]]



---
### References:

- [json — JSON encoder and decoder — Python 3.14.8 documentation](https://docs.python.org/3/library/json.html#module-json)