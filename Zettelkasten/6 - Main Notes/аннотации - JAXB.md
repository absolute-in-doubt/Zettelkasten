
04-11-2025 13:05

Status: #adult 

Tags: [[JAXB]]

---
# аннотации - JAXB

![[Pasted image 20251104130515.png]]
![[Pasted image 20251104130529.png]]

### Class-level Annotations

#### @XmlRootElement(name = "department") 

Указывает, что класс Department является корневым элементом XML. Параметр name задаёт имя корневого элемента; если не указан, используется имя класса. В данном случае корневой элемент будет . Эта аннотация обязательна для маршалинга объекта в XML.

#### @XmlAccessorType(XmlAccessType.FIELD) 

Определяет, что JAXB будет работать напрямую с полями класса (игнорируя геттеры и сеттеры). Это упрощает обработку данных и автоматически включает все аннотированные и неаннотированные поля в XML. 

```java
@XmlRootElement(name = "department") 
@XmlAccessorType(XmlAccessType.FIELD) 
public class Department
```



### Field-level Annotations (for collection fields)

#### @XmlElementWrapper(name = "employees") 

Создаёт обёртку с тегом вокруг всех элементов коллекции сотрудников. Это позволяет сгруппировать сотрудников в один блок XML под этим тегом.

#### @XmlElement(name = "employee")

Определяет, что каждый элемент коллекции (сотрудник) будет представлен в XML с тегом внутри обёртки .


```java
@XmlElementWrapper(name = "employees") 
@XmlElement(name = "employee") 
private List
```


### Field-level annotations

#### @XmlTransient

Исключает поле (или класс) из сериализации

----
#### [[аннотации - JAXB - Flashcards|Link to flashcards]]



---
### References:

