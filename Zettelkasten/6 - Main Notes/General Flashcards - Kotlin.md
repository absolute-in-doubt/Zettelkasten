
Theory for the cards: [[Kotlin]]

FILE TAGS: kotlin

Q: Расскажи о синтаксических отличиях Kotlin от Java.
A: В Kotlin: 
- функции не обязаны привязываться к классу
- не нужно ставить `;` после каждого "sentence" (they're optional)
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
<!--ID: 1778683004357-->
