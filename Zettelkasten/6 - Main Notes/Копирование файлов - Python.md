
04-10-2026 16:47

Status:

Tags: [[Python]]

---
# Копирование файлов - Python


#### Копирование текстовых файлов

Тут все банально:

```python
with open('initial.txt', 'r') as rf:
	with open('copy.txt', 'w') as wf:
		for line in rf:
			wf.write(line)
```


### Для бинарных файлов и изображений


```python
with open('initial.jpg', 'rb') as rf:
	with open('copy.jpg', 'wb') as wf:
		chunk_size = 4096
		rf_chunk = rf.read(chunk_size)
		while len(rf_chunk) > 0:
			wf.write(rf_chunk)
			rf_chunk = rf.read(chunk_size)
```

- Добавляем `b` к типу открытия файла, чтобы он открылся в бинарном формате

----
#### [[Копирование файлов - Python - Flashcards|Link to flashcards]]



---
### References:

