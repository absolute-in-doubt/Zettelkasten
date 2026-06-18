
Theory for the cards: [[Non-terminal & terminal operations - Stream API]]

FILE TAGS: java+ stream_api

Q: Что тут не так?
```java
Stream<String> stream = list.stream();
	
Stream<String> uppercaseStream = stream.map(el -> el.toUpperCase());
	
Stream<String> lowercaseStream = stream.map(el -> el.toLowerCase());
	
lowercaseStream.forEach( el -> System.out.println(el));
```
A:     
```java
Stream<String> stream = list.stream();
	
Stream<String> uppercaseStream = stream.map(el -> el.toUpperCase()); //здесь исходный stream уже пуст
	
/* 
операция на уже пустом (использованном) Stream:
IllegalStateException: stream has already been operated upon or closed 
*/
Stream<String> lowercaseStream = stream.map(el -> el.toLowerCase());
	
lowercaseStream.forEach( el -> System.out.println(el));
```
<!--ID: 1770794344216-->


Q: Чем в Stream API отличаются map и flatMap?
A:   
- `map(Function<T,R> func)`: один входной элемент -> один выходной элемент; сохраняет структуру потока. Применяет функцию, возвращающую любой объект, например T -> R.
	
- `flatMap(Function<T, Stream<R>)`: один входной элемент -> поток выходных элементов; расплющивает вложенные структуры в один единый поток. Применяет функцию, возвращающую поток элементов, например `T -> Stream<R>`.
<!--ID: 1770798897656-->

