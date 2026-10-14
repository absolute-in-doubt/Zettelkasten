
04-10-2026 17:06

Status:

Tags: [[OS module - Python]] [[Python]]

---
# Создание и удаление директорий - OS Python


> [!note] **Создание файлов**
> Файлы создаются через обычный `open` с параметром `w`:
> ```python
> home_dir = os.environ.get('HOME')  
>file_path =  os.path.join(home_dir, 'test.txt')  
>with open(file_path, 'w') as f:  
>    pass
> ```

### Создание директории


Создание одной поддиректории без вложенности:

```python
os.mkdir('OS-Demo-2') # Создаст новую поддиректорию в текущей директории
```

> [!tip] Note
> Это вернет ошибку:
> ```python
> os.makedir('OS-demo-2/Sub-Dir-1')
> ```
> В обычном `mkdir` нельзя создавать вложенные директории.


Создание нескольких поддиректорий:

```python
os.makedirs('Os-Demo-2/Sub-Dir-1')
```

~={orange}**Проще всегда использовать `os.makedirs` - не ошибешься.**=~


---
### Удаление директорий

Удаление только самой глубокой директории:

```python
os.rmdir('OS-Demo-2/Sub-Dir-1') # Удалит только Sub-Dir-1
```


Удаление всех директорий:

```python
os.removedirectories('OS-Demo-2/Sub-Dir-1') # Удалит и Sub-Dir-1 и OS-Demo-2
```

~={orange}**Для удаления лучше всегда использовать `os.rmdir`, чтобы не удалить случайно нужное**=~


----
#### [[Создание и удаление директорий - OS Python - Flashcards|Link to flashcards]]



---
### References:

