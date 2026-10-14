
24-09-2026 20:17

Status:

Tags: [[JVM Diagnostics & Monitoring]] [[Java+]]

---
# Анализ heap dump в Eclipse MAT - JVM Diagnostics & Monitoring


**Eclipse Memory Analyzer (MAT)** — специализированный инструмент для анализа heap dump.

Открываешь:

```
File → Open Heap Dump → heapdump.hprof
```

После загрузки я бы смотрел:

- **Leak Suspects Report** — автоматический поиск подозрительных объектов.
- **Histogram** — какие классы занимают больше всего памяти.
- **Dominator Tree** — кто фактически удерживает память.
- **Path to GC Roots** — почему конкретный объект не может быть удалён GC.

Для поиска Memory Leak особенно важна связка:

```
Dominator Tree
       ↓
крупный объект
       ↓
Path to GC Roots
       ↓
кто удерживает этот объект
```


### В Heap Dump ищем не просто «самые большие объекты»

Главный вопрос:

> **Почему эти объекты вообще остаются достижимыми?**

Например MAT показывает:

```
HashMap
  └── 5 000 000 User
```

Тогда смотрим **Path to GC Roots**:

```
GC Root
  ↓
Singleton
  ↓
Cache
  ↓
HashMap
  ↓
User
```

И обнаруживаем:

```
static Map<Long, User> cache = new HashMap<>();
```

где отсутствует eviction.

Это уже потенциальная причина.


----
#### [[Анализ heap dump в Eclipse MAT - JVM Diagnostics & Monitoring - Flashcards|Link to flashcards]]



---
### References:

