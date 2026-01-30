
21-01-2026 20:23

Status: #baby 

Tags: [[Multithreading]] [[Java+]]

---
# ReadWriteLock - Multithreading Java

`ReadWriteLock` — интерфейс из `java.util.concurrent.locks`, предоставляющий **две связанные блокировки**: `readLock()` для **многократного конкурентного чтения** и `writeLock()` для **эксклюзивной записи**. 

Основная реализация — `ReentrantReadWriteLock`


### Основная идея (аналогично [[StampedLock - Multithreading Java|StampedLock]])

| Держит            | Может взять `readLock()` | Может взять `writeLock()` |
| ----------------- | ------------------------ | ------------------------- |
| **никто**         | ✅ **много**              | ✅**только один**          |
| **`readLock()`**  | ✅ **много**              | ❌ **ждёт**                |
| **`writeLock()`** | ❌ **ждёт**               | ❌ **ждёт**                |

```
[читатели=0, писатели=0] → readLock() → [читатели=1, писатели=0]
                           ↓
[читатели=1, писатели=0] → readLock() → [читатели=2, писатели=0] ✅
                           ↓
[читатели=2, писатели=0] → writeLock()→ ЖДЁТ (ждёт всех читателей)

```


----
#### [[ReadWriteLock - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

