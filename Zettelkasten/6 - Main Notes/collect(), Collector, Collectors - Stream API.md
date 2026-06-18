
11-02-2026 12:21

Status:

Tags: [[Stream API]] [[Java+]]

---
# collect(), Collector, Collectors - Stream API

`collect()`  - терминальная операция Stream API, собирающая элементы в коллекцию/структуру. Принимает `Collector<T, A, R>` —  интерфейс с 4 методами:

```java
public interface Collector<T, A, R> {
    Supplier<A> supplier(); //создает пустой аккумулятор (контейнер результата)
    BiConsumer<A, T> accumulator(); //добавляет элемент в аккумулятор
    BinaryOperator<A> combiner(); //объединяет 2 аккумулятора (для parallelStream)
    Function<A, R> finisher(); //финализирует результат
    // + 2 default метода: characteristics(), custom...
}
```

Обычно просто передаём реализации этих методов в `collect` (без явного создания Collector):
```java
// 2. С тремя функциями (чаще всего используется) 
R collect(Supplier<R> supplier, 
		  BiConsumer<R, ? super T> accumulator, 
		  BiConsumer<R, R> combiner)
```

Пример реализации:
```java
// Собираем строки в LinkedList (аналог Collectors.toList())
Collector<String, List<String>, List<String>> toLinkedList = 
    Collector.of(
        () -> new LinkedList<String>(),           // supplier
        List::add,                                // accumulator  
        (left, right) -> { left.addAll(right); return left; }, // combiner
        x -> x                                    // finisher (identity)
    );

// Использование
List<String> result = Stream.of("a", "b", "c")
    .collect(toLinkedList);

```

#### Реализации Collector из Collectors:

Сбор в базовые коллекции:
```
toList()          → List<T> (ArrayList)
toSet()           → Set<T> (HashSet, без дубликатов)  
toCollection(c)   → любой Collection
toMap(key, value) → Map<K,V>
toConcurrentMap() → thread-safe Map
```

Группировка:
```
groupingBy(classifier) → Map<K, List<T>> groupingBy(classifier, toList()) → Map<K, List<T>> (явно)
```

Хз как это обозвать:
```
joining()          → String (элементы через "")
joining(delimiter) → String с разделителем
summingInt(Long/Double) → числовая сумма
averagingInt()     → OptionalDouble среднее
summarizingInt()   → IntSummaryStatistics (min/max/avg/count/sum)
counting()         → Long количество[web:31][web:36]
```


---
### Custom collectors

```
Collector.of(supplier, accumulator, combiner, finisher)
```

### Примеры:

```java
// Список → Map
Map<String, Long> nameCount = users.stream()
  .collect(groupingBy(User::getName, counting()));

// Группировка + статистика  
Map<String, IntSummaryStatistics> statsByDept = 
  employees.stream()
  .collect(groupingBy(Employee::getDept, 
      summarizingInt(Employee::getSalary)));
```

----
#### [[collect(), Collector, Collectors - Stream API - Flashcards|Link to flashcards]]



---
### References:

