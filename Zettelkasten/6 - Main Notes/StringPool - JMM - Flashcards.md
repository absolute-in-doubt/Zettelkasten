
Theory for the cards: [[StringPool - JMM]]

FILE TAGS: java_interview

Q: Что тут происходит? Что будет выведено на экран?
```java
String s1 = "Harry Potter";  
String s2 = "The Lord of the Rings";  
String s3 = "Harry Potter";
	
String s4 = new String("Harry Potter");  
String s5 = new String("The Lord of the Rings");
	
System.out.println(s1==s3);
System.out.println(s1==s4);
	
s4 = s4.intern();  
	  
System.out.println(s1==s4);
```
A:   
```java
String s1 = "Harry Potter";  
String s2 = "The Lord of the Rings";  
String s3 = "Harry Potter";
	
String s4 = new String("Harry Potter");  
String s5 = new String("The Lord of the Rings");
	
System.out.println(s1==s3); //true
System.out.println(s1==s4); //false
	
s4 = s4.intern();  
	  
System.out.println(s1==s4); //true
```
	
> [!note] 
> Оператор `==` сравнивает адреса в памяти!!
	
- Создание объекта через `new String("...")` всегда создаёт НОВЫЙ объект String - **НЕ ПОМЕЩАЯ ЕГО В String Pool**
- метод `intern()` привяжет литерал к String pool и **возвратит привязанное значение**
<!--ID: 1776188781298-->
