
09-10-2025 20:55

Status: #baby 

Tags: [[Patterns]]

---
# Factory / Factory method - pattern

Static метод, который содержит в себе код создания объекта класса

> [!note] **Factory method обычно создаёт не себя, а объекты других классов**

Factory method подразумевает наличие отдельного абстрактного класса (интрефейса) с абстрактным factory method:
- Реализации этого класса (интрефейса) - Factory classes
- Каждый Factory class содержит реализацию Factory method


![[Pasted image 20251009210037.png]]

1) **Product** - общий интерфейс для классов, возвращаемых фабричным методом
	
2) **Concrete Products** - реализации **Product**
	
3) **Creator** - интерфейс, определяющий сигнатуру фабричного метода. Этот метод возвращает созданный объект типа **Product** (родительский интерфейс)
	
4) **Concrete Creators** - Factory classes, которые реализуют **Creator** -> имеют factory method нужной сигнатуры (возвращает созданный объект типа **Product**)

---

## Пример реализации

```java
interface Transport {
    void deliver();
}
	
class Truck implements Transport {
    public void deliver() {
        System.out.println("Deliver by land in a box");
    }
}
	
class Ship implements Transport {
    public void deliver() {
        System.out.println("Deliver by sea in a container");
    }
}
	
	
// какркас factory класса
abstract class Logistics {
    public void planDelivery() {
        Transport t = createTransport(); // factory method
        t.deliver();
    }

    protected abstract Transport createTransport();
}
	
//Factory class fot Trucks
class RoadLogistics extends Logistics {
    @Override
    protected Transport createTransport() {
        return new Truck(); // создаём объект другого класса
    }
}
	
//Factory class for Ships
class SeaLogistics extends Logistics {
    @Override
    protected Transport createTransport() {
        return new Ship();
    }
}

```


---
### Примеры

Реализации `BeanFactory` interface:

```java
public interface BeanFactory {
	
    getBean(Class<T> requiredType);
    getBean(Class<T> requiredType, Object... args);
    getBean(String name);
    // ...
]
```
	
**Each of the _getBean_ methods is considered a factory method**
	
> [!note]
>`ApplicationContext` extends `BeanFactory`

Примеры реализации `BeanFactory`:

- `DefaultListableBeanFactory` - Базовая, поддерживает аннотации (@Autowired), XML
- `ClassPathXmlApplicationContext` 
- `AnnotationConfigApplicationContext`

----
#### [[Factory method - pattern - Flashcards|Link to flashcards]]



---
### References:

