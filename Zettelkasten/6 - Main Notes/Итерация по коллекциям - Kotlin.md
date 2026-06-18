
13-05-2026 20:14

Status:

Tags: [[Kotlin]]

---
# Итерация по коллекциям - Kotlin




### `for` - аналог `enhanced for` в Java

```kotlin
val ia = intArrayOf(1,2,3)  
  
for (a in ia) {  
    println(a)  
}
```

#### Итерация по Map

```kotlin
var map = mapOf(1 to "Kotlin", 2 to "Java", 3 to "Python", "What" to "the fuck")  
  
for((key, value) in map){  
    println("$key -> $value")  
}
```

----
### Итерация по диапазону (аналог обычного `for` в Java)


```kotlin
for (a in 1..9) {  //выведет числа от 1 до 9 ВКЛЮЧИТЕЛЬНО
    println(a)  
}  
  
for(a in 1 until 9) {  //выведет числа от 1 до 9 НЕ ВКЛЮЧИТЕЛЬНО
    println(a)  
}
```

#### Указание шага при итерации по диапазону

```kotlin
for (a in 1..9 step 2) {  //1,3,5,7,9
    println(a)  
}  
  
for(a in 1 until 9 step 2) {  //1,3,5,7
    println(a)  
}
```


##### Итерация по диапазону с декрементом

```kotlin
for(a in 9 downTo 2){  
    println(a)  
}
```

Output: 
	От 9 ВКЛЮЧИТЕЛЬНО до 2 ВКЛЮЧИТЕЛЬНО

----
#### [[Итерация по коллекциям - Kotlin - Flashcards|Link to flashcards]]



---
### References:

