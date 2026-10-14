
04-10-2026 17:24

Status:

Tags: [[OS module - Python]] [[Python]]

---
# Чтение env значений переменных - OS Python

`OS` - именно для чтения значений. А для загрузки env переменныйх из `.env` файла в контекст (чтобы их можно было прочитать с помощью `OS`) - реализуется с помощью [[dotenv - Python lib]].

```python
home_directory = os.environ.get('HOME')
```


----
#### [[Чтение env значений переменных - OS Python - Flashcards|Link to flashcards]]



---
### References:

