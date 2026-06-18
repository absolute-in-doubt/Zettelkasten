
11-02-2026 17:00

Status:

Tags:

---
# Record - Java

(появились в java 17)

~={red}**Immutable**=~

Все поля record - тоже final.

- **Can't extend classes.** - сами неявно наследуют Record
- **Can't be extended**  
- ~={green}**Can implement interfaces**=~

---

Automatically creates getter methods:

```java
public record Address(String city, String street,  int house){
	
	public static void main(String[] args) {  
		Address addr = new Address("NYC", "5th av.", 17);
		String city = addr.city()   
		   
	}
}
```

`+` Automaticlly generates НОРМАЛЬНЫЕ `toString()`, `hashCode()`, `equals()` implementations.

Например, автоматически созданный `equals()` вернёт true только если обе record имеют одинаковые значения всех полей.

---
### Overriding default constructor

(для добавления логики при создании)

```java
public record Address(String city, String street,  int house){
	
	Address(String city, String street,  int house){
		System.out.println("suuuuuu");
		this.city = city;
		this.street = street;
		this.house = house;
	}
}
```

Только один конструктор с тему же параметрами, что определены сверху.


##### Compact constructor

Тот же конструктор, но можем пропустить утсановку значений:

```java
public record Address(String city, String street,  int house){
	
	Address /*тут ничего не указываем*/ {
		System.out.println("suuuu")
		// значения полей установятся автоматически
	}
	
	public static void main(String[] args) {  
		Address addr = new Address("NYC", "5th av.", 17);
		String city = addr.city()   
		   
	}
}
```




---
#### Как и в обычном классе можно определять/переопределять методы & static методы

```java
public record Address(String country, String city, String street, String house){  
    @Override  
    public boolean equals(Object obj) {  
        if(obj instanceof Address) {  
            Address addr = (Address) obj;  
            return this.city.equals(addr.city())   
                    && this.country.equals(addr.country())  
                    && this.street.equals(addr.street())  
                    && this.house.equals(addr.house());  
        }  
        return false;  
    }
}
```


~={red}**Но нельзя определять поля**=~ - только те, что определены сверху

----
#### [[Record - Java - Flashcards|Link to flashcards]]



---
### References:

