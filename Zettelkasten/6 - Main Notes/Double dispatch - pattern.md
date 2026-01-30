
15-01-2026 10:17

Status:

Tags: [[Patterns]]

---
# Double dispatch - pattern


## Static (early) binding

Static binding (раннее связывание) — выбор вызываемого метода на этапе компиляции, по **типу ссылки** и сигнатуре.​

Основные моменты:

- Используется для `static`, `final`, `private` методов и ~={cyan}перегрузки методов=~ (overloading).
    
- Компилятор по типам аргументов и типу переменной однозначно выбирает реализацию, и во время исполнения уже ничего не доопределяется.
    

Следствие: вызов статического метода или приватного метода всегда однозначно понятен по исходному коду, полиморфизма там нет.​

## Runtime (dynamic) binding

Runtime (dynamic, late) binding — выбор реализации _во время выполнения_ по **фактическому объекту**, на который указывает ссылка.

Основные моменты:

- Это то, что в Java обычно называют динамическим полиморфизмом через ~={cyan}переопределение=~ (overriding).
    
- Для нестатических, не `final` и не `private` методов таблица виртуальных методов позволяет во время выполнения найти реализацию в фактическом классе объекта.
    

Следствие: 
```java
Animal a = new Dog();
a.speak();
```

Выбор конкретной `speak()` происходит в рантайме по объекту `Dog`, а не по типу ссылки `Animal`.​


---
## Связь с перегрузкой и переопределением

- ~={cyan}Перегрузка (overloading) → static binding=~: выбор перегруженного метода однозначно делает компилятор.
    
- ~={cyan}Переопределение (overriding) → runtime binding=~: метод уже выбран по сигнатуре, но конкретная реализация выбирается в момент вызова по реальному объекту.

Часто говорят, что ~={yellow}«каждый вызов сначала проходит статическое связывание (выбор перегрузки), а затем — при необходимости — динамическое (выбор реализации при overridden‑методах)»=~.​

## Double dispatch

Single dispatch (обычный случай в Java) означает: выбор метода основан на _одном_ динамическом типе — получателя (`this`), а типы параметров учитываются только на этапе компиляции.​

Double dispatch — шаблон, при котором фактическая реализация определяется сразу по _двум_ динамическим типам (обычно двух объектов), часто через паттерн «Посетитель» (Visitor).​

Идея:

- Первый объект вызывает метод второго, передавая себя (`other.interactWith(this)`), что даёт первый динамический тип.
    
- Внутри у второго есть перегруженные `interactWith(...)` для разных типов первого, и через обычный динамический вызов + перегрузку имитируется зависимость от двух типов.
    

В Java double dispatch достигается не языковой конструкцией, а комбинацией переопределения и перегрузки (Visitor, обработка столкновений в играх и т.п.).​

```java
// 1. Элементы, которые «посещают»
interface Shape {
    void accept(ShapeVisitor visitor);
}

class Circle implements Shape {
    @Override
    public void accept(ShapeVisitor visitor) {
        // static binding
        visitor.visit(this);
    }
}

class Rectangle implements Shape {
    @Override
    public void accept(ShapeVisitor visitor) {
        //static binding
        visitor.visit(this);
    }
}

// 2. Visitor с перегруженными методами
interface ShapeVisitor {
    void visit(Circle circle);
    void visit(Rectangle rectangle);
}

// 3. Конкретный Visitor
class AreaCalculator implements ShapeVisitor {
    @Override
    public void visit(Circle circle) {
        System.out.println("Вычисляем площадь круга");
    }

    @Override
    public void visit(Rectangle rectangle) {
        System.out.println("Вычисляем площадь прямоугольника");
    }
}

// 4. Использование
public class Main {
    public static void main(String[] args) {
        Shape circle = new Circle();
        Shape rectangle = new Rectangle();
        ShapeVisitor visitor = new AreaCalculator();

        circle.accept(visitor);    // Динамически определяет тип circle (т.к. ссылка на него имеет тип Shape)
        rectangle.accept(visitor);  // Динамически определяет тип rectangle (т.к. ссылка на него имеет тип Shape)
    }
}

```

> [!tip] Note
> Мы не можем сделать так:
> ```java
> visitor.visit(circle); 
> ```
> потому что выбор перегруженного метода происходит **во время компиляции** и компилтор смотрит на **статические типы** аргументов (то есть как они объявлены в коде: `Shape circle`, а не фактический `new Circle()`);

----
#### [[Double dispatch - pattern - Flashcards|Link to flashcards]]



---
### References:

- [[Алгоритм динамического связывания, vtable класса - Java Core]]