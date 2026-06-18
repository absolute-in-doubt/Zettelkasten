
13-05-2026 20:26

Status:

Tags: [[Kotlin]]

---
# Extension function - Kotlin

Позволяют создавать новые методы к существующим классам **без наследования** и **без изменения исходного кода** класса

Например вот метод для класса String который мы объявили вообще в main функции:

```kotlin
fun main(){  
	
    fun String.getEmotion(): String {  
        return when {  
	        //.last() - возвращает последний символ строки
            last() == '!' -> "Exciting"  // неявно this.last()
            last() == '?' -> "Curious"   
            last() == '.' -> "Calm"  
            else -> "Unidentified"  
        }  
    }  
	  
    val s = "How are you?"  
    println(s.getEmotion())  
	  
}
```


---
### **Особенности**

- **`this`** внутри extension-функции ссылается на объект, для которого вызывается функция.
- Extension-функции **не изменяют класс**. Они компилируются как обычные статические методы.
- **Не могут переопределять существующие методы класса**, если такой метод уже есть — вызовется оригинальный метод.


----
#### [[Extension function - Kotlin - Flashcards|Link to flashcards]]



---
### References:

