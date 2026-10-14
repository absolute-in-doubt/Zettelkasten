
04-10-2026 17:21

Status:

Tags: [[OS module - Python]] [[Python]]

---
# Передвижение по директориям - OS Python



#### Передвижение по директориям

```python
os.getcwd() # выведет current working directory

os.chdir('/Users/нео/Desktop/') # Перейти в директорию

os.listdir() # Вернет список поддиректорий в текущей директории
os.listdir('/Users/') # Вернет список поддиректорий в /Users
print(os.listdir())

```


### Traversing directory tree

```python
os.walk()
```

Для каждой директории, которую он видит в текущей, выводит :
- path
- subdirectories
- files in the directory

```python
for dirpath, dirnames, filenames in os.walk(os.getcwd() + '/utils'):  
    print(f'Current Path: {dirpath}')  
    print(f'Subdirectories: {dirnames}')  
    print(f'Files: {filenames}\n')
```

Output:
```
C:\python-workspace\demoProject\.venv\Scripts\python.exe C:\python-workspace\demoProject\main.py 
Current Path: C:\python-workspace\demoProject/utils
Subdirectories: ['__pycache__']
Files: ['csv_utils.py', 'file_utils.py', 'json_utils.py']

Current Path: C:\python-workspace\demoProject/utils\__pycache__
Subdirectories: []
Files: ['csv_utils.cpython-312.pyc', 'file_utils.cpython-312.pyc', 'json_utils.cpython-312.pyc']
```

~={orange}**Проходит через всё дерево рекурсивно**=~

---
### Конкатенация названия директории

Т.к. присоединении сложно запомнить с какой стороны должын быть символы `/`, `\` и нужны ли они вообще, можно использовать метод:

```python
path = os.path.join(home_dir, 'test.txt')
```



----
#### [[Передвижение по директориям - OS Python - Flashcards|Link to flashcards]]



---
### References:

