
21-01-2026 18:51

Status:

Tags: [[Multithreading]] [[Java+]]

---
# Locks - Multithreading Java


- **[[synchronized - Multithreading Java|synchronized блок]]**: это ключевое слово в Java, которое может использоваться для ограничения доступа к коду только одним потоком. Этот блок может быть использован для синхронизации методов или блоков кода.

- **[[ReentrantLock - Multithreading Java|ReentrantLock]]**: это класс, реализующий интерфейс Lock, который предоставляет более гибкие возможности блокировки. Он позволяет использовать более сложные сценарии блокировки, такие как блокировка с ожиданием определенного времени, блокировка с попыткой прервать ожидание и другие.

- **[[ReadWriteLock - Multithreading Java|ReadWriteLock]]**: это интерфейс, предоставляющий две блокировки - одну для чтения и одну для записи. Эта схема позволяет нескольким потокам выполнять операции чтения одновременно, но блокирует доступ на запись во время обновления данных.

- **[[StampedLock - Multithreading Java|StampedLock]]**: lock с поддержкой write/read и ultra-fast _[[optimistic & pessimistic locks - Multithreading Java#Оптимистическая блокировка|optimistic read]]_, который ускоряет чтение и снижает contention в многопоточности.


|Характеристика|`synchronized`|`ReentrantLock`|`ReadWriteLock`|`StampedLock`|
|---|---|---|---|---|
|**Читатели**|1 поток|1 поток|**N потоков**|N потоков|
|**Реентрант**|✅|✅|✅ read/write|❌|
|**RW семантика**|❌|❌|✅|✅|
|**Optimistic**|❌|❌|❌|✅|
|**Condition**|wait/notify|✅|✅ (отдельно)|❌|
|**Сложность**|Низкая|Средняя|Высокая|Очень высокая|


----
#### [[Locks - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

