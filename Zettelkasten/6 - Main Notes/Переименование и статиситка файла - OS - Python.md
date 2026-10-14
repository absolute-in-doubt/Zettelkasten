
04-10-2026 17:23

Status:

Tags: [[OS module - Python]] [[Python]]

---
# Переименование и статиситка файла - OS - Python


### Переименование файла

```python
os.rename('test.txt', 'new_name.txt')
```



### Получение информации о файле

```python
os.stat('demo.txt') # Возвращает объект os.stat_result

print(os.stat('demot.txt'))
```

Пример os.stat_result:
```
os.stat_result(st_mode=33206, st_ino=25332747904403964, st_dev=17166150934882621371, st_nlink=1, st_uid=0, st_gid=0, st_size=55, st_atime=1791042959, st_mtime=1791042936, st_ctime=1791042936)
```

Тут из полезного:

- `st_size` - размер файла

Пример использования:

```python
if f.is_file and os.stat(f).st_size > 0
	# Проверка, пуст ли файл
```

----
#### [[Переименование и статиситка файла - OS - Python - Flashcards|Link to flashcards]]



---
### References:

