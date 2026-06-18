
08-12-2025 16:32

Status:

Tags: [[MinIO]]

---
# MinIO cnsole client commands

~={red}!!!!=~ ~={orange}**Здесь используется alias `docker_minio` для обращения к серверу.**=~ ~={red}!!!!=~


### **TL;DR:**
__
	- _cp_: Copy a file or object between file systems.
	- _ls_: List files or objects in a bucket.
	- _mb_: Create a bucket (similar to _mkdir_ on Linux).
	- _mv_: Move/relocate a file or object from one file system to another.
	- _rb_: Remove a bucket (similar to _rmdir_ on Linux).
	- _rm_: Remove a file or object.

```bash
$ mc mb user1
$ mc cp ~/Resume.pdf user1
$ mc mb user2
$ mc cp user1/Resume.pdf user2
$ mc rb user1
$ mc ls user2
[2023-05-15 21:39:10 MDT]     491K Resume.pdf
$ mc rm myminio/my-bucket/my-file.txt
```


#### List buckets:

```bash
mc ls docker_minio
```

### Создание бакета через alias:

```bash
mc mb docker_minio/your-bucket-name
```




## Managing access to the directories:

- **Запретить анонимный доступ (по умолчанию):**

```bash
mc anonymous set none myminio/mybucket
```

→ bucket закрыт, доступ только по учётным данным.


- **Разрешить скачивание (чтение) объектов:**

```bash
mc anonymous set download myminio/mybucket
```

→ любой может читать и скачивать объекты, но не загружать.


- **Разрешить загрузку (запись) объектов:**

```bash
mc anonymous set upload myminio/mybucket
```

→ любой может загружать объекты, но не читать их.

### Проверка текущей политики:

```bash
mc anonymous get myminio/mybucket
```


----
#### [[MinIO cnsole client commands - Flashcards|Link to flashcards]]



---
### References:

