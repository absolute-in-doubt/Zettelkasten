
22-09-2025 18:00

Status: #baby

Tags: [[Системный анализ]]

---
# UML диаграмма классов


----
#### [[UML диаграмма классов - Flashcards|Link to flashcards]]


### Поля

Поля обычно указываются таким образом:

```css
visibility name : type
```

Пример:

```diff
- founder : String
- employees : List<Employee>
```


### Методы

```css
visibility name(parameterList) : returnType
```

Пример:

```diff
+ getFounder() : String
+ addEmployee(emp : Employee) : void
```



## Связи


![[Pasted image 20250922180436.png]]


-  Ассоциация -~={cyan} хранит ссылку=~ на другой класс ~={cyan}как поле=~
	- жизненные циклы классов могут быть не связаны
	
- Зависимость - класс использует другой класс~={cyan} только внутри метода=~:
	(не хранит ссылку на другой класс и не включает его в свою структуру)
	
	```java
		class Printer {
	    // зависимость — объект используется только внутри метода
	    public void print(Document doc) {
	        System.out.println("Печать: " + doc.getText());
	    }
	}
	```


> [!note]
> Агрегация и Композиция  -  подтипы ассоциации.
> 


- Композиция - "части принадлежат только одному целому и не могут существовать без него"
	- класс хранитель удаляется -> удаляются все внутренние элементы
	- для такого обычно используются [[Inner Classes Java|Inner классы]]
	
- Агрегация - объект состоит из других, но они могут существовать независимо"
	- ЖЦ классов не связан: если удалить "целое", его "части" могут продолжить существовать.
	- когда один объект по смыслу является частью другого, но может существовать отдельно
	- частный случай ассоциации
	
	📌 Пример из реального мира:
	- Университет и студенты.  
    Если университет ликвидируют, студенты всё равно существуют.

> [!note]
> На UML диаграмме при изображении агрегации композиции:
> Ромб -  со стороны владеющего класса.

> [!warning]
> В программной реализации агрегация выглядит так же как и ассоциация


```java
class IncludeClass {
	B b; 
	E e; 
	D d;
	public IncludeClass(E e) {
		this.e=e;//агрегация
		d=new D(); //композиция 
	}
	
	public void fC(C c) {c.fC();}; //зависимость
	public void fB(B b) {this.b=b; b.fB(); ;};  /*ассоциация (многие к одному)*/
	public void fD() {d.fD();}
	public void fE() {e.fE();}
	
}

 class B{
	 IncludeClass ic [];
	
	 public void IC(IncludeClass ic []){
		 this.ic=ic;
	 } 
	 
	 public void fB() {
		 System.out.println("Привет от fB, класс "+getClass().getName());
	 }
 }

 class D{
	 public void fD() {
		 System.out.println("Привет от fD, класс "+getClass().getName());
	 }
}

 class E{
	 public void fE() {
		 System.out.println("Привет от fE, класс " +getClass().getName());
	 }
}

 class C{
	 public void fC() {
		 System.out.println("Привет от fC, класс "+getClass().getName());
	 }
}
```


### Направление стрелок

![[Pasted image 20250930092625.png]]


---
### References:
