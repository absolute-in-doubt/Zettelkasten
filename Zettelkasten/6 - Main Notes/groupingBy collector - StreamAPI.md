
25-03-2026 19:34

Status:

Tags:

---
# groupingBy collector - StreamAPI



#### 1. Простая группировка

```java
Map<K, List<T>> result = stream.collect(Collectors.groupingBy(classifier));
```

Где `classifier` — функция `Function<? super T, ? extends K>`, определяющая ключ группы. По умолчанию значения собираются в `List`.projava-code.github+1

**Пример:**

```java
List<Employee> employees = ...;
Map<Department, List<Employee>> byDept = 
    employees.stream().collect(Collectors.groupingBy(Employee::getDepartment));
```


---
#### 2. С downstream-коллектором

```java
Map<K, D> result = stream.collect(Collectors.groupingBy(classifier, downstream));
```

`downstream` — коллектор для обработки элементов каждой группы (например, `toSet()`, `counting()`).

**Примеры:**

```java
// Уникальные значения в Set
Map<Department, Set<Employee>> byDeptUnique = 
    employees.stream().collect(Collectors.groupingBy(
        Employee::getDepartment, 
        Collectors.toSet()));

// Подсчет количества
Map<Department, Long> countByDept = 
    employees.stream().collect(Collectors.groupingBy(
        Employee::getDepartment, 
        Collectors.counting()));

// Сумма зарплат
Map<Department, Integer> salarySum = 
    employees.stream().collect(Collectors.groupingBy(
        Employee::getDepartment, 
        Collectors.summingInt(Employee::getSalary)));
```


> [!warning] Note
> Тут используется unbound ссылка на метод


Чтобы использовать как ключ сам объект - можно вызвать `Function.identity()` - метод возвращающий input параметр:

```java
String sentence = "I am learning Streams API in Java Java";  
  
System.out.println(Arrays.stream(sentence.split(" "))  
        .collect(Collectors.groupingBy(Function.identity(),
        Collectors.counting()
        )
	)
);
```



---
#### 3. С mapSupplier (полная сигнатура)

```java
Map<K, D> result = stream.collect(Collectors.groupingBy(classifier, mapSupplier, downstream));
```

`mapSupplier` — функция создания `Map` (например, `HashMap::new`, `ConcurrentHashMap::new`).

**Пример:**

```java
Map<Department, Long> countByDept = 
    employees.stream().collect(Collectors.groupingBy(
        Employee::getDepartment,
        ConcurrentHashMap::new,  // thread-safe
        Collectors.counting()));
```


----
#### [[groupingBy collector - StreamAPI - Flashcards|Link to flashcards]]



---
### References:

