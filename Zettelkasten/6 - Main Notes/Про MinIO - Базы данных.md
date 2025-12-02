
02-12-2025 11:04

Status:

Tags: [[MinIO]] [[Базы данных]] [[Java+]]

---
# Про MinIO - Базы данных


**MinIO — это объектное хранилище**, совместимое с Amazon S3 API.

Документы (файлы) хранятся как **объекты** внутри **бакетов**.


Каждый объект имеет:

- **ключ (object key)** — уникальное имя внутри бакета (например, `index.html` или `static/js/app.js`),

- **метаданные** (например, `Content-Type: text/html`),

- **содержимое (payload)** — сам файл.


---
### Файловая система

Аналогична Unix системам, но:

- директории (Unix) -> ~={orange}buckets=~ (MinIO)
- файлы (Unix) -> ~={orange}objects=~ (MinIO)

**Можно создавать иерархии bucket-ов:**
```
/
/images/
  imge1.png
  image2.jpg
/videos/
  video1.mp4
/users/
  /john.doe/
    3rd quarter revenue report.docx
```

**Buckets могут иметь контроль доступа** Т.е. можно настраивать, кот может получить доступ к конкретному bucket.


---



----
#### [[Про MinIO - Базы данных - Flashcards|Link to flashcards]]



---
### References:

