
24-09-2026 19:04

Status:

Tags: [[JVM Diagnostics & Monitoring]] [[Java+]]

---
# Диагностика OutOfMemoryException - JVM Diagnostics & Monitoring



> **Понять, какая память закончилась → снять heap dump → найти, кто удерживает объекты → найти место в коде, которое их удерживает → проверить, почему GC не может их освободить.**


### 1. Сначала определить тип OOM

В сообщении ошибки обычно есть подсказка:

```
java.lang.OutOfMemoryError: Java heap space
java.lang.OutOfMemoryError: GC overhead limit exceeded
java.lang.OutOfMemoryError: Metaspace
java.lang.OutOfMemoryError: Direct buffer memory
java.lang.OutOfMemoryError: unable to create native thread
```

Это принципиально важно: **не любой OOM означает переполненный Java Heap**.


---
### 2. Если `Java heap space` — проверить GC

Смотрим:

```
Heap Used
Heap Max
GC frequency
GC pause
```

Инструменты:

- **JConsole / VisualVM** — быстро посмотреть память.
- **JFR / Mission Control** — production profiling.
- `jstat` — состояние heap/GC.
- GC logs — например:
    
    ```
    -Xlog:gc*:file=gc.log
    ```
    

Главный вопрос:

```
После Full GC:

Used
████████████████████ 95%

или

Used
██████               30%
```

Если после Full GC память **не освобождается и постепенно растёт** → очень вероятна утечка/неограниченное удержание объектов.


---
### 3. Снять Heap Dump

При OOM удобно заранее включить:

```
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/path/heapdump.hprof
```

Получаем:

```
heapdump.hprof
```

Открываем в:

- **Eclipse MAT**
- **VisualVM**
- IntelliJ Profiler
- JProfiler

Для серьёзного анализа я бы в первую очередь использовал **Eclipse MAT**.

---

### 4. В Heap Dump ищем не просто «самые большие объекты»

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

---

### 5. Типичные причины

**Коллекция растёт бесконечно:**

```
static List<Event> events = new ArrayList<>();
```

**Cache без ограничения:**

```
Map<Key, Value>
```

**ThreadLocal не очищается:**

```
threadLocal.set(largeObject);
```

**Очередь/буфер постоянно растёт:**

```
producer >>> consumer
```

**Слишком много объектов создаётся одновременно:**

```
repository.findAll(); // 10 млн записей
```

**Memory leak через listeners/subscriptions.**

**Слишком большой object graph из-за неправильного lifecycle.**

---

### 6. Если Heap нормальный — смотреть другие виды памяти

Например:

```
Java Heap       → Java heap space
Metaspace       → Metaspace
Direct Memory   → Direct buffer memory
Native memory   → native allocations / threads
```

Для native memory полезен:

```
-XX:NativeMemoryTracking=summary
```

и:

```
jcmd <pid> VM.native_memory summary
```

Если:

```
unable to create native thread
```

смотрим количество потоков и их stack memory:

```
jcmd <pid> Thread.print
```

---

## Практический алгоритм

```
OOM
 │
 ├─ 1. Какой именно OOM?
 │
 ├─ 2. Если Heap?
 │      │
 │      ├─ GC освобождает память?
 │      │       ├─ Да → возможно, просто недостаточный -Xmx
 │      │       └─ Нет → подозрение на leak
 │      │
 │      └─ 3. Heap Dump
 │
 ├─ 4. MAT
 │      │
 │      ├─ Dominator Tree
 │      ├─ Biggest Retained Heap
 │      └─ Path to GC Roots
 │
 └─ 5. Найти владельца объектов
        ↓
      cache / collection / ThreadLocal /
      queue / static / listener
        ↓
      исправить lifecycle
        ↓
      повторно измерить
```

### Что сказать на собеседовании

> **Сначала определяю тип OOM. Для `Java heap space` смотрю GC logs и динамику heap: если после Full GC память не возвращается — снимаю heap dump. Анализирую его в Eclipse MAT через Dominator Tree и Path to GC Roots, нахожу объекты с большим retained heap и кто их удерживает. Если heap не является причиной — отдельно проверяю Metaspace, Direct Memory и native memory через JFR/jcmd и соответствующие метрики.**



---
# Тестирование для выявления причин OOM


**1. Load / stress test — основной вариант**

Постепенно увеличиваем нагрузку:

```
100 req/s → 200 → 500 → 1000 → ...
```

и смотрим:

```
Heap Used
GC
Количество объектов
Latency
```

Если после каждого цикла нагрузка снимается, а:

```
Heap: 40% → 55% → 70% → 85% → 95%
```

не возвращается обратно — подозрение на memory leak.

---

**2. Soak test / длительный тест**

Очень полезен именно для утечек:

```
нагрузка
   ↓
1 час
   ↓
несколько часов
```

Например:

```
Heap после GC:

10 мин → 35%
30 мин → 42%
1 час  → 55%
2 часа → 72%
```

Постепенный рост при стабильной нагрузке — сильный признак утечки или неограниченного накопления данных.

----
#### [[Диагностика OutOfMemoryException - JVM Diagnostics & Monitoring - Flashcards|Link to flashcards]]



---
### References:

