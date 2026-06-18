
11-02-2026 12:01

Status:

Tags: [[Stream API]] [[Java+]]

---
# Виды stream и создание экземпляров - Stream API

**Stream** — последовательная обработка элементов в одном потоке. Гарантирует порядок выполнения операций.​

**parallelStream** — параллельная обработка с использованием ForkJoinPool (по умолчанию количество потоков = число ядер CPU). Разбивает данные на части (Spliterator), выполняет параллельно, затем объединяет результаты; порядок не гарантирован.


**Ключевые отличия:**

- Производительность: parallelStream ускоряет CPU-bound задачи на больших коллекциях (>10k элементов), но overhead может замедлить малые данные или I/O-операции.​
    
- Порядок: Stream сохраняет, parallelStream — нет (идеально для независимых операций как filter/map/sum).​
    
- Создание parallel: `collection.parallelStream()` или `stream().parallel()` — второй всегда даёт параллельный поток.


---
### Способы создания Stream

- **Из коллекций:** `list.stream()` или `list.parallelStream()​
    
- **Из массивов:** `Arrays.stream(array)​
    
- **Из значений:** `Stream.of(1, 2, 3)​
    
- **Бесконечные:** `Stream.generate(() -> Math.random()).limit(10)` или `Stream.iterate(0, n -> n + 1).limit(10)​
    
- **Из файлов:** `Files.lines(Paths.get("file.txt"))​
    
- **Пустой:** `Stream.empty()`

> [!warning]
> В Java нет CharStream - по причине редкого использования:
> Используем `IntStream`. Получение `IntStream` из строки:
> ```java
> myString.chars(); //returns IntStream
> ```
> Преобразование обратно в строку из `IntStream`:
> ```java
> str.chars().distinct().toString();
> ```

----
#### [[Виды stream и создание экземпляров - Stream API - Flashcards|Link to flashcards]]



---
### References:

