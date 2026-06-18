
14-02-2026 14:50

Status:

Tags: [[PostgreSQL DB]] [[Базы данных]]

---
# Реализация репликации - PostgreSQL


![[Pasted image 20260214145019.png]]

- startup - процесс, применяющий изменения из wal log, обычно выполняется при старте БД. При физической репликации startup зацикливается и применяет изменения из WAL log, а WAL log receiver обновляет этот WAL log.

---

![[Pasted image 20260214145239.png]]




----
#### [[Репликация - PostgreSQL - Flashcards|Link to flashcards]]



---
### References:

- [[Диспетчер журналов (WAL) - PostgreSQL]]
- [[Репликация]]