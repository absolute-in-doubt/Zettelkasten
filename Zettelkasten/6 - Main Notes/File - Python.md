
03-10-2026 12:00

Status:

Tags: [[Python]] 

---
# File - Python


### Working with files

```python
f = open('workfile', 'w', encoding="utf-8")
	# reading

f.close() 

```

-  `'r'` - the file will only be read, 
- `'w'` - only writing **~={orange}(an existing file with the same name will be erased)=~**,  
-  `'a'` opens the file for appending; any data written to the file is automatically added to the end. 
- `'r+'` opens the file for both reading and writing

> [!note]
> Appending a `'b'` to the mode opens the file in _binary mode_. Binary mode data is read and written as [`bytes`](https://docs.python.org/3/builtins/stdtypes.html#bytes "bytes") objects. You can not specify _encoding_ when opening file in binary mode.


~={red}**Calling `f.write()` without using the `with` keyword or calling `f.close()` **might** result in the arguments of `f.write()` not being completely written to the disk, even if the program exits successfully.**=~

### `with`

Аналог `try-with-resources`.

The advantage is that the file is properly closed after its suite finishes, even if an exception is raised at some point. Using `with` is also much shorter than writing equivalent `try-finally` blocks:

```python
with open('workfile', encoding="utf-8") as f:
    read_data = f.read()

# We can check that the file has been automatically closed.
f.closed
```




---
### `read(size)`

- `size` - optional. Если не указан - читаем все.

```python
with open('test.txt', 'r', encoding="utf-8") as f:
	size_to_read = 100
	
	f_contents = f.read(size_to_read)
	
	while len(f_contents) > 0:
	print(f_contents, end='')
	f_contents = f.read(size_to_read)
	
```



### `readline()`

Читает до символа `\n`

Также сожно итерировать по строкам файла (использует `readline()` implicitly)

```python
for line in f:
    print(line, end='')
```


> [!tip]
> If you want to read all the lines of a file in a list you can also use `list(f)` or `f.readlines()`.


#### `readlines()`

```python
with open('test.txt', 'r', encoding="utf-8") as f:
	f_contents = f.readlines() # returns a list of lines
```


---
### `write(string)`

Returrns the amount of characters written to the file.

```python
f.write('This is a test\n') # returns 15
```

Типы должны приводиться к string:

```python
value = ('the answer', 42)
s = str(value)  # convert the tuple to string
f.write(s)
```


---
### Передвижение по файлу


### `seek(offset, whence)`

- передвинет указатель на `offset` количество позиций

- `whence` - value of 0 measures from the beginning of the file, 1 uses the current file position, and 2 uses the end of the file as the reference point.

```python
f = open('workfile', 'rb+')
f.write(b'0123456789abcdef')

f.seek(5)      # Go to the 6th byte in the file

f.read(1)

f.seek(-3, 2)  # Go to the 3rd byte before the end

f.read(1)
```


> [!tip] **Прикол с `seek` при `write`**
>```python
>with opne('test.txt', 'w', encoding="utf-8") as f: 
>	f.write('Test#1')
>	f.seeek(0)
>	f.write('John')
>```
>Сначала запишет в файл `Test#1`, потом вернется назад и перезапишет первые 4 символа. В результате в файле будет:
>```
>John#1
>```


#### `tell()`

Возвращает текущее занчение оффсета


----
### Прверка, пуст ли файл

Через отдельную либу:

```python
from pathlib import Path

p = Path("file.txt")

if p.is_file() and p.stat().st_size == 0:
    print("Файл существует и пустой")
elif p.is_file():
    print("Файл существует и не пустой")
else:
    print("Файл не существует")
```



----
#### [[File - Python - Flashcards|Link to flashcards]]



---
### References:

- [[IO - Python]]

- https://docs.python.org/3/tutorial/inputoutput.html