
02-12-2025 11:52

Status:

Tags: [[MinIO]] [[Базы данных]] [[Java+]]

---
# MinIO Java SDK


### MinioClient

Предназначен для выполнения фактически всех операций с БД:
- создание/удаление buckets
- добавление/получение/удаление objects


#### Создание MinioClient

```java
MinioClient minioClient =
  MinioClient.builder()
    .endpoint("http://127.0.0.1:9000")
    .credentials("minioadmin", "minioadmin")
    .build();
```


#### Создание bucket

```java
minioClient.makeBucket(
  MakeBucketArgs
    .builder()
    .bucket("user1")
    .build());
```


#### Загрузка объекта в bucket

```java
minioClient.putObject(PutObjectArgs
  .builder()
  .bucket("user1")
  .object("Resume.pdf")
  .stream(new FileInputStream("/tmp/Resume.pdf")
  .build());
```


#### Получение объекта из bucket

```java
minioClient.putObject(PutObjectArgs
  .builder()
  .bucket("user1")
  .object("Resume.pdf")
  .stream(new FileInputStream("/tmp/Resume.pdf")
  .build());
```


#### Генерация Presigned URL на объект в MinIO

При таком подходе сервер не взаимодействует с MinIO напрямую, а просто создаёт временные ссылки на объекты в MinIO и редиректит пользователя по ним.

~={orange}Нужно для ограничения доступа к файлу, например, если для доступа нажна авторизация.=~

```java
String url = minioClient.getPresignedObjectUrl(
    GetPresignedObjectUrlArgs.builder()
        .method(Method.GET)
        .bucket("mybucket")
        .object("report.pdf")
        .expiry(60 * 10) // 10 минут
        .build());
```

Если никакой авторизации не нужно - обычно просто перенаправляют к файлу. Без Presigned URL. Это называется reverse проксирование.


----
#### [[MinIO Java SDK - Flashcards|Link to flashcards]]



---
### References:

