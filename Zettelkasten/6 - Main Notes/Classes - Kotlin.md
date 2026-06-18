
13-05-2026 20:43

Status:

Tags: [[Kotlin]]

---
# Classes - Kotlin

**~={red}К полям класса нужно обращаться напрямую. Getters and setters вызываются implicitly.=~**

### Old fashioned:

```kotlin
class Person {  
    var firstName: String = "John"  
    var lastName = "Doe"  
    var age: Int = 19  
}
```

### New way - похоже на `record` НО НЕ immutable

```kotlin
class Person (  
    var firstName: String = "John",  
    var lastName: String = "Doe",  
    var age: Int = 19  
)
```

Если есть методы, нужно добавлять `constructor` key word:


---
### Secondary constructors

Каждый вторичный конструктор должен делегировать создание дефолтному конструктору 
Это реализуется при помощи `: this()`:

```kotlin
class Person (  
    var firstName: String = "John",  
    var lastName: String = "Doe",  
    var age: Int = 19  
) {  
    val fullName: String  
  
    get() = "$firstName $lastName"  
  
    constructor(years: Int) : this() {  
        age += years  //Возьмет default value age и увеличит на 3
    }  
}
```




----
### Custom class fields

Поля, для которых мы хотим определить кастомное поведение getter-ов и setter-ов не надо включать в default constructor.

Для полей default конструктора используется getter и setter по умолчанию

```kotlin
class Person (  
    var firstName: String = "John",  
    var lastName: String = "Doe",  
    var age: Int = 19  
) {  
    val fullName: String  
  
    get() = "$firstName $lastName"  
}
```


----
#### [[Classes - Kotlin - Flashcards|Link to flashcards]]



---
### References:

