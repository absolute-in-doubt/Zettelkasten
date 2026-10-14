
Theory for the cards:  [[Диагностика OutOfMemoryException - JVM Diagnostics & Monitoring]]

FILE TAGS:  JVM_diagnostics interview_fuckups java_interview

Q: С помощью какого-нибудь тестирования можно понять причину OutOfMemoryException?
A: Я бы использовал stress/load (циклический тест с высокой нагрузкой и после каждого прогона проверяем Heap Used, GC, Количество объектов, Latency) и особенно длительный soak test (стабильная нагрузка несколько часов подряд и мониторим, повышается ли использование Heap Used, GC, Количество объектов, Latency). 
	
При воспроизведении включил бы:
	
```
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/path/heapdump.hprof
```
	
И можно также добавить флаг `-Xmx128m` - ограничит максимальный размер heap (до 128 Mb), чтобы меньше ждать, но тут надо быть осторожным, ориентируясь на то сколько памяти должно потреблять приложение при нормальной работе.
	
Получил бы heap dump и анализировал его в Eclipse MAT через Dominator Tree и Path to GC Roots.
<!--ID: 1790343754900-->


Q: Как выявить причину `OutOfMemoryException`?
A:   
В первую очередь определить тип OOM:
```
java.lang.OutOfMemoryError: Java heap space
java.lang.OutOfMemoryError: GC overhead limit exceeded
java.lang.OutOfMemoryError: Metaspace
java.lang.OutOfMemoryError: Direct buffer memory
java.lang.OutOfMemoryError: unable to create native thread
```
	
	
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
<!--ID: 1790343754915-->

Q: Какие могут быть причины у `OutOfMemoryException`?
A:   
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
<!--ID: 1790343999094-->

Q: Может ли БД быть причиной OOM?
A: Да. **БД вполне может быть косвенной причиной `OutOfMemoryError` в Java-приложении.** Сама БД обычно не «съедает» heap JVM, но приложение может вытащить из неё слишком много данных или накопить результаты запросов.
	
Типичные случаи:
	
- **`SELECT` без пагинации** → `findAll()` возвращает миллионы записей → объекты занимают heap.
    
- **Большой `JOIN`** → огромный result set → JDBC/JPA удерживает много объектов.
    
- **Не закрывается `ResultSet` / `Stream`** → ресурсы и данные остаются удерживаемыми.
    
- **Hibernate persistence context** → тысячи/миллионы загруженных entity остаются managed внутри `Session`.
    
- **Batch processing без `flush/clear`** → Hibernate постепенно накапливает entity в persistence context.
    
- **Медленная БД → очередь запросов растёт** → application threads/очереди держат запросы, DTO и контекст в памяти.
    
- **Connection pool / async queue** → при проблемах с БД могут накапливаться задачи, ожидающие выполнения.
    
- **Кэширование результатов запросов** → неограниченный cache может постепенно заполнить heap.
    
	
---
**Как отличить**
	
Например:
	
```text
DB query
   ↓
10 млн строк
   ↓
JPA/JDBC
   ↓
миллионы Java objects
   ↓
Heap → OOM
```
	
При расследовании я бы проверял:
	
1. **GC/heap** — что именно растёт.
    
2. **Heap dump → MAT** — какие объекты занимают память.
    
3. **SQL / query execution** — нет ли огромных result set.
    
4. **JPA/Hibernate** — persistence context, batching, pagination.
    
5. **Connection pool / очереди** — нет ли накопления запросов.
    
6. **Нагрузочный тест** — воспроизводится ли OOM на конкретном сценарии.
    
**Главное:** если в heap dump видишь много `Entity`, `DTO`, `ArrayList`, `HashMap`, JDBC/Hibernate-объектов — БД-запрос вполне может быть первопричиной. Но нужно найти **что удерживает эти объекты**, а не просто обвинять БД.
<!--ID: 1790344950938-->
