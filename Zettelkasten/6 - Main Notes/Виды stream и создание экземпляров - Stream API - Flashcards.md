
Theory for the cards: [[Виды stream и создание экземпляров - Stream API]]

FILE TAGS: stream_api java+

Q: В Java нет CharStream, как нам работать с char в streams? Если например нужно избавиться от повторяющихся букв в слове? Как нам пройти слово в стриме?
A:   
 Используем `IntStream`. Получение `IntStream` из строки:
```java
myString.chars(); //returns IntStream
```
	
Преобразование обратно в строку из `IntStream`:
```java
str.chars().distinct().toString();
```
<!--ID: 1770801238089-->
