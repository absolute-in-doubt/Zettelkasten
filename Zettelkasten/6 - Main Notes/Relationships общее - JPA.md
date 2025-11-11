
29-10-2025 22:12

Status: #baby 

Tags: [[Hibernate & JPA]]

---
# Relationships общее - JPA

Для аннотаций типа отношения

- параметр mappedBy="owner'sFieldName" - ставится в аннотации Parent класса, т.е. НЕ на владельце отношения.

- параметр cascade -может указываться как на владельце отношения так и не на владельце




@JoinColumn - аннотация владельца отношения



**Реализовывать отношение с двух сторон -~={green} хорошая практика.=~**



**Поддерживать объекты приложения и объекты в БД в одном состоянии - ~={green}хорошая практика=~** (т.к. Parent объекты в кэше не обновляются при добавлении к ним Child объектов)

Вручную добавляем сохранённые объекты в поле сущности Parent класса:
~={green}**делаем это именно в методе Parent  класса (НЕ владельца отношения)**:=~
```java
class Person {
	
	public void setPassport(Passport passport){  
	    this.passport = passport;  
	    passport.setPerson(this);  
	}
	
	...
}
```

----
#### [[Relationships общее - JPA - Flashcards|Link to flashcards]]



---
### References:

