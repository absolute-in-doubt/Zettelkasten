
Theory for the cards:  [[Method references - Stream API]]

FILE TAGS: stream_api java+

Q: Какие есть типы ссылок на методы определённого объекта?
A:    
- Bound - привязывается к объекту в момент создания ссылки:
```java
StringBuilder sb = new StringBuilder("initial-string-");
Function<Integer, StringBuilder> appendFunc = sb::append;
	
 appendFunc.apply(5);
 sb.toString(); //initial-string-5
```
	
- Unbound - привязывается к объекту непосредственно в момент выполнения (передаём объект как аргумент)
```java
BiFunction<StringBuilder, Integer, StringBuilder> appendFunc = StringBuilder::append;
	
StringBuilder sb = new StringBuilder("initial-string-");
appendFunc.apply(sb, 5);
sb.toString(); //initial-string-5
```
<!--ID: 1770815901918-->

