
Theory for the cards: [[collect(), Collector, Collectors - Stream API]]

FILE TAGS: stream_api java+

Q: Чем отличаются reduce и collect в Stream API? КОгда что применять?
A:  
- `reduce(R initialVal, Function<T [result], T [element])` - инициализирует объект `result` значением `intialVal`, после чего на каждую итерацию применяет Function к элементу. **На выходе получается одно значение того же тапа, которого и был stream:**
  `T -> T`
	
- `collect(Collector.of(supplier, accumulator, combiner, finisher))`:
```java
public interface Collector<T, A, R> {
    Supplier<A> supplier(); //создает пустой аккумулятор (контейнер результата)
    BiConsumer<A, T> accumulator(); //добавляет элемент в аккумулятор
    BinaryOperator<A> combiner(); //объединяет 2 аккумулятора (для parallelStream)
    Function<A, R> finisher(); //финализирует результат
    // + 2 default метода: characteristics(), custom...
}
```
Может возвращать результат любого типа.
Пример:
```java
public static String removeDuplicatesFromStringWithCollector(String str){  
    return str.chars().distinct().mapToObj(x -> (char) x)  
            .collect(  
                    Collector.of(  
                            StringBuilder::new,  
                            (sb , ch) -> sb.append(ch),  
                            (sb1, sb2) -> sb1.append(sb2),  
                            (sb) -> sb.toString()  
                )  
            );  
}
```
<!--ID: 1770805329655-->
