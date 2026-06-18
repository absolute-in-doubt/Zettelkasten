
11-02-2026 10:04

Status:

Tags: [[Stream API]] [[Java+]]

---
# Non-terminal & terminal operations - Stream API

- **Non-terminal** operations - добавляют этапы обработки элементов стрима, but executed ==lazily==.

- **Terminal** operations - trigger the execution of a stream (все non-terminal operations + сама terminal операция). 


> [!warning]
> После вызова любой операции на стриме, исходный объект stream становится пустым и unusable:
> ```java
Stream<String> stream = list.stream();
>	
>Stream<String> uppercaseStream = stream.map(el -> el.toUpperCase()); //здесь исходный stream уже пуст
>	
>/* 
>операция на уже пустом (использованном) Stream:
>IllegalStateException: stream has already been operated upon or closed 
>*/
>Stream<String> lowercaseStream = stream.map(el -> el.toLowerCase());
>	
>lowercaseStream.forEach( el -> System.out.println(el));
>```
>
>```java
>Stream<String> uppercaseStream = stream.map(el -> el.toUpperCase()); //здесь исходный stream уже пуст
>	
>// так можно:
>Stream<String> lowercaseStream = uppercaseStream.map(el -> el.toLowerCase());
>```


---

![[Pasted image 20260211105826.png]]

#### Non-Treminal operations

- `map(Function<T,R> func)`: один входной элемент -> один выходной элемент; сохраняет структуру потока. Применяет функцию, возвращающую любой объект, например T -> R.

- `flatMap(Function<T, Stream<R>)`: один входной элемент -> поток выходных элементов; расплющивает вложенные структуры в один единый поток. Применяет функцию, возвращающую поток элементов, например `T -> Stream<R>`.


##### Terminal operations

- `max(Comparator<T>)`: возвращает максимальный элемент стрима, обёрнутый в `Optional<T>`. **~={green}Special case of reduction=~**
Пример:
```java
String longestWord = Arrays.stream(words)
.max(Comparator.comparing(s -> s.length())).get();
```

- `reduce(T identity, Function<T [result], T [element]>)`: че тут говорить, структура сложная, вот пример:
```java
String longestWord = 
Arrays.stream(sentence.split("[ ,.]"))
.reduce("", (result, s) 
	-> (s.length() > result.length())? s : result );
```

Тут:
- `identity` - изначальное значение `result`

- `result` - хранится в самом фрейме операции reduce и передаётся в каждую итерацию вместе с новым элементом. 
  Итоговое значение `result` возвращается по завершению `reduce` - **~={red}не оборачивается в `Optional<T>`.=~**

- `element` - элемент, на каждую итерацию новый.


----
#### [[Non-terminal & terminal operations - Stream API - Flashcards|Link to flashcards]]



---
### References:

