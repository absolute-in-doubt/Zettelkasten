
04-10-2025 09:49

Status: #baby 

Tags: [[Patterns]]

---
# Prototype - pattern

Реализуется переопределением метода [[Object class - Java Core|clone() + Clonable]] marker interface.

Используется для клонирования объекта.

~={red}!!!=~ ~={yellow}Реализация Prototype - сущность, которая умеет себя копировать.=~ ~={red}!!!=~

![[Pasted image 20251004095439.png]]

---


![[Pasted image 20251004095452.png]]


## Пример кода:

```java
interface PersonProto<T>{
	public T clone();
}

class Student
implements PersonProto<Student>{
@Override
public Student clone(){
return new Student();}
}
```



----
#### [[Prototype - pattern - Flashcards|Link to flashcards]]



---
### References:

