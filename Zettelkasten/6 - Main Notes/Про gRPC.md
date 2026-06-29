
04-01-2026 18:36

Status:

Tags: [[gRPC]] [[Java+]]

---
# Про gRPC

gRPC (Google Remote Procedure Call) — современный высокопроизводительный фреймворк RPC поверх HTTP/2, использующий бинарный формат Protocol Buffers.

![[Pasted image 20260210103716.png]]

Только тут не один пакет, а несколько. Т.к. gRPC использует HTTP/2, который передаёт одно сообщение фреймами (по частям).


---

- Interface Definition Language - использует protobuffs для задания стандартизированных интерфейсов.

- Благодаря использованию HTTP/2 может использовать [[HTTP 1.1 & HTTP 2 - Networks#1. Основные отличия **HTTP/1.1** и **HTTP/2**|мультиплексирование]], уменьшая задержку

- Bidirectionl streaming

- Независимость от языка программирования




----
#### [[Про gRPC - Flashcards|Link to flashcards]]



---
### References:

