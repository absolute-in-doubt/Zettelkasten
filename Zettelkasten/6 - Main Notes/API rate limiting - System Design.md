
23-03-2026 19:04

Status:

Tags: [[System Design]]

---
# API rate limiting - System Design

Предотвраещет DOS атаки с одного IP:

Контролирует количество запросов, которое пользователь может сделать к API на протяжении определённого промежутка времени



### Пример

![[Pasted image 20260324110150.png]]

1. Пользователь делает запрос к API Getaway;
2. API Getaway смоттрит в Redis по userId, есть ли у пользователя достаточное количество токенов, чтобы сделать запрос;
3. Если у пользователя достаточное количество токенов - делает запрос к серверу;
4. Если же недостаточно токенов - возвращает HTTP 429 Too Many Requests

5. Admin может вносить изменения в политику лимитов: сообщение об изменении ложем в броекр сообщений
6. Worker читает сообщения и обновляет (via an HTTP Request) политику лимитов (in-memory configuration of the API Getaway):



----
#### [[API rate limiting - System Design - Flashcards|Link to flashcards]]



---
### References:

