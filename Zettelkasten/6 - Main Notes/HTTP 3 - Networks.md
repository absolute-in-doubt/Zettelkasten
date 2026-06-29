
27-06-2026 19:40

Status:

Tags: [[Networks]]

---
# HTTP 3 - Networks

Из-за того, что HTTP/2 использует один TCP-коннект для множества потоков, возникает проблема **Head-of-Line Blocking**:

```
Потерялся один TCP-сегмент
           ↓
TCP не может передать последующие данные приложению
           ↓
Все HTTP/2 потоки внутри этого соединения ждут
```

Именно поэтому в HTTP/3 отказались от TCP и перешли на UDP + QUIC.


----
#### [[HTTP 3 - Networks - Flashcards|Link to flashcards]]



---
### References:

