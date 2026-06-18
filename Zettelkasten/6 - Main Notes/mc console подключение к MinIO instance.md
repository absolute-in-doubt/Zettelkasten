
07-03-2026 15:53

Status:

Tags: [[MinIO]]

---
# mc console подключение к MinIO instance

> [!note] У меня `mc` стоит на хосте (Windows)
> Подключаем через проброшенный порт 9000.


 Для подключения к MinIO instance через консольный клиент `mc` (MinIO Client) выполните команду `mc alias set`:

```
mc alias set myminio http://your-minio:9000 YOUR_ACCESS_KEY YOUR_SECRET_KEY
```

----
#### [[mc console подключение к MinIO instance - Flashcards|Link to flashcards]]



---
### References:

