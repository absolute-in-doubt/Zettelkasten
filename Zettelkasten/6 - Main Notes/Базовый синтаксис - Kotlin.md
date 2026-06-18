
13-05-2026 11:11

Status:

Tags: [[Kotlin]]

---
# Базовый синтаксис - Kotlin


 - функции не обязаны привязываться к классу

- не нужно ставить `;` после каждого "sentence" (they're optional)




### Переменные 

- иначе иннициализируются переменные:
```kotlin
val name = "kotlin"  // неизменяемая переменная (константа)
var language = "java"  // обычная переменная
```

- Можно explicitly указать тип переменной:
```kotlin
val name : String = "kotlin"  
var language : String = "java"
```
	
> [!note] Kotlin строго типизирован
> Если переменная была инициализирована как `var language = "java"`, ей уже нельзя присвоить число

Kotlin строго типизированный, просто он сам автоматически ставит тип при создании переменной.


#### Null-safety

Переменная не может хранить null, если мы explicitly не задали это.

Пример создания nullable переменной:
```kotlin
var nullable : String? = "python"  
nullable = null
```


----
### String templates

```kotlin
val name :String = "kotlin"  
  
println("Hello, $name!")
```


### Complex template

Позволяет выполнять операции и вызывать функции, возвращая результат в строку:

```kotlin
println("Complex template\noutput: ${1+2}")
```

output:
```
Complex template
output: 3
```


---
### Array

В kotlin это обычный класс. Когда мы вставляем и получаем данные при помощи `arr[i]` под капотом вызываются методы `get()` и `set()` соответствующего класса.

Kotlin содержит отдельный классы для массивов каждого примитивного типа: IntArray, ByteArray, ShortArray ...

```kotlin
val arr = arrayOf(1,2,3,4)  
println(arr[0])  
println(arr.size)  
for (i in arr){  
    println(i)  
}
```

Пример явного задания:

```kotlin
val ia : IntArray = intArrayOf(1,2,3,4)
```


---
### Conditional operators

##### `if`

Вполне обычный:
```kotlin
if(age >= 21){  
    println("Welcome to the Beerland")  
} else {  
    println("You are not allowed here")  
}  
```

Но немного уебищный однострочный тернарный оператор:
```kotlin
val result = if (age >= 21) "Welcome to the Beerland" else "You are not allowed here"  
  
println(result)
```



### `when` - аналог `switch-case`

```kotlin
println("Enter the day of the week")  
var dayOfWeek = readLine();  
  
when (dayOfWeek) {  
    "Saturday" -> println("Today is Saturday!")  
    "Sunday" -> println("Today is Sunday!")  
    else -> println("Today is a weekday!")  
}

var food = when (dayOfWeek) {  
    "Saturday" -> "pizza"  
    "Sunday" -> "pasta"  
    else -> "salad"  
}

food = when {  
    dayOfWeek == "Saturday" -> "pizza"  
    dayOfWeek == "Sunday" -> "pasta"  
    else -> "salad"  
}
```

> [!warning] 
> No breaks here



```kotlin
```






----
#### [[Базовый синтаксис - Kotlin - Flashcards|Link to flashcards]]



---
### References:

