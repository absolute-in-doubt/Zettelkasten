
03-10-2026 12:29

Status:

Tags: [[Python]]

---
# csv - Python

`csv.reader(csvfile, /, dialect='excel', **fmtparams)`

```python
import csv
with open('eggs.csv', newline='') as csvfile:
    spamreader = csv.reader(csvfile, delimiter=' ', quotechar='|')
    for row in spamreader:
        print(', '.join(row))
        
Spam, Spam, Spam, Spam, Spam, Baked Beans
Spam, Lovely Spam, Wonderful Spam
```

where `eggs.csv` contains:

```
Spam Spam Spam Spam Spam |Baked Beans|
Spam |Lovely Spam| |Wonderful Spam|
```

> [!note]
> Чтобы при каждом `writerow` два раза не обрабатывался newline при открытии файла нужно прописывать `newline=''`:
> ```python
> open(filename, 'a', encoding='utf-8', newline='')
> ```
>
>### Почему так происходит?
>
>`csv.writer` сам добавляет стандартный разделитель строк CSV — `\r\n`:
>
>```
>Apple,Jack,Buick\r\n
>```
>
>А `open()` на Windows по умолчанию выполняет **перевод newline при записи**. В результате `\r\n` от `csv.writer` может дополнительно преобразовываться, и получается фактически двойной перенос:
>```
>Apple,Jack,Buick\r\n\r\n
>```




`csv.writer(csvfile, /, dialect='excel', **fmtparams)`

```python
import csv
with open('eggs.csv', 'w', newline='') as csvfile:
    spamwriter = csv.writer(csvfile, delimiter=' ',
                            quotechar='|', quoting=csv.QUOTE_MINIMAL)
    spamwriter.writerow(['Spam'] * 5 + ['Baked Beans'])
    spamwriter.writerow(['Spam', 'Lovely Spam', 'Wonderful Spam'])
```

which writes `eggs.csv` containing:

```
Spam Spam Spam Spam Spam |Baked Beans|
Spam |Lovely Spam| |Wonderful Spam|
```

----
#### [[csv - Python - Flashcards|Link to flashcards]]



---
### References:

- [csv — CSV File Reading and Writing — Python 3.14.8 documentation](https://docs.python.org/3/library/csv.html)