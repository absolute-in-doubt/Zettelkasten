
03-09-2025 14:08

Status: #adult 

Tags: [[Java Core]]

---
# Передача по значению в Java

В Java параметры всегда передаются только по значению, что определяется как «скопировать значение и передать копию». С примитивами это будет копия содержимого. Со ссылками - тоже копия содержимого, т.е. копия ссылки. При этом внутренние члены ссылочных типов через такую копию изменить возможно, а вот саму ссылку, указывающую на экземпляр - нет.


```java
class Circle{
	public int x;
	public int y;
	
	public Circle(int x, int y){
		this.x = x;
		this.y = y;
	}
	
	public void move(Circle circle){
		circle.x++; // изменит значение x у myCircle
		circle = new Circle(0,0); //указатель circle теперь указывает на созданный объект
	}
	
	
	public static void main(String[] args){
		Circle myCircle = new Circle(5, 5);
		move(myCircle);
		System.out.println(myCircle.x); // выведет 6
	}
}
```
> [!warning]
> В функцию передается копия указателя ~={purple}(адрес объекта Circle но в другой ячейке памяти)=~ и, если создать новый объект, то изменится лишь значение в новой ячейке памяти, а адрес хранимый оригинальным указателем -  не изменится.

---
### [[Передача по значению в Java - Flashcards|Link to the Flashcards]]