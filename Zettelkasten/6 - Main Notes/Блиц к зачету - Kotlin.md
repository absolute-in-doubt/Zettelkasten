
Theory for the cards: [[Kotlin]]

FILE TAGS: kotlin



Q: Типы данных Kotlin
A:   
Отличие от Java: все типы являются объектами на уровне языка, есть nullable/non-null версии.
	
```kotlin
val a: Int = 10
val b: Double = 10.5
val c: Boolean = true
val d: Char = 'A'
val e: String = "Hello"
	
val x: Int? = null
```
	
Основные: `Byte, Short, Int, Long, Float, Double, Boolean, Char, String`
<!--ID: 1778782722589-->


Q: Переменные Kotlin
A: `val` — неизменяемая, `var` — изменяемая.
	
```kotlin
val name = "John"
var age = 20
	
age = 25
```
	
Можно явно указывать тип:
	
```kotlin
val x: Int = 5
```
<!--ID: 1778782722595-->



Q: Операторы Kotlin
A: То же самое, что и в Java.
	
Дополнительно:
	
```kotlin
a in list
a !in list
	
x ?: y     // Elvis
x?.length  // safe call
```
<!--ID: 1778782722600-->


Q: Что делает оператор `?.` (safe call)?  
A: Безопасно обращается к свойству или методу объекта. Если объект `null`, результатом будет `null`, а исключение не возникнет.
	
```kotlin
val length = str?.length
```
<!--ID: 1778782722606-->

Q: Что делает оператор `?:` (Elvis)?  
A: Возвращает значение слева, если оно не `null`, иначе возвращает значение справа.
	
```kotlin
val name = userName ?: "Unknown"
```
	
Если `userName == null`, будет `"Unknown"`.
<!--ID: 1778782722610-->

Q: Стандартный ввод / вывод Kotlin
A:    
```kotlin
println("Hello")
	
val name = readln()
```
	
Число:
	
```kotlin
val x = readln().toInt()
```
<!--ID: 1778782722615-->



Q: Преобразование типов Kotlin
A: Автоматического расширения нет.
	
```kotlin
val x: Int = 10
val y: Long = x.toLong()
```
	
Есть:
	
```kotlin
toInt()
toDouble()
toString()
toLong()
```
<!--ID: 1778782722619-->


Q: Соглашение о выражениях, операторах и блоках
A: Многие конструкции являются выражениями и возвращают значение.
	
```kotlin
val max = if(a>b) a else b
```
	
Последняя строка блока возвращается:
	
```kotlin
val x = run {
    val a=5
    a+10
}
```
<!--ID: 1778782722624-->


Q: Выражение Kotlin if-else
A:   
`if` возвращает значение.
	
```kotlin
val max = if(a>b) a else b
```
	
```kotlin
val result = if(a>b){
    a+1
}else{
    b+1
}
```
<!--ID: 1778782722628-->


Q: Цикл while
A: То же самое, что и в Java.
	
```kotlin
while(x<5){
    x++
}
```
<!--ID: 1778782722633-->


Q: Цикл do-while
A: То же самое, что и в Java.
	
```kotlin
do{
    x++
}while(x<5)
```
<!--ID: 1778782722637-->


Q: Цикл for
A: Работает через итераторы и диапазоны.
	
```kotlin
for(i in 1..5){
    println(i)
}
```
	
Другие варианты:
	
```kotlin
for(i in 1 until 5)
	
for(i in 10 downTo 1)
	
for(i in 1..10 step 2)
```
<!--ID: 1778782822845-->


Q: When в Kotlin
A: Аналог `switch`, но мощнее.
	
```kotlin
when(x){
    1 -> println("one")
    2 -> println("two")
    else -> println("other")
}
```
	
Как выражение:
	
```kotlin
val result = when(x){
    1 -> "one"
    else -> "other"
}
```
	
Проверка типов:
	
```kotlin
when(obj){
    is String -> println(obj.length)
}
```
<!--ID: 1778782722642-->


Q: Оператор Break
A: То же самое, что и в Java.
	
```kotlin
while(true){
    break
}
```
	
Метки:
	
```kotlin
loop@ for(i in 1..5){
    break@loop
}
```
<!--ID: 1778782722646-->


Q: Оператор Continue
A: То же самое, что и в Java.
	
```kotlin
for(i in 1..10){
    if(i==5) continue
}
```
	
С метками:
	
```kotlin
continue@loop
```
<!--ID: 1778782722651-->


Q: Массивы
A:   
```kotlin
val arr = arrayOf(1,2,3) //создаёт Array<Int>
	
println(arr[0])
	
arr[0]=10
```
	
Примитивные массивы:
	
```kotlin
val nums = intArrayOf(1,2,3) // создаёт IntArray
```
<!--ID: 1778782722656-->


Q: чем метод arrayOf(1,2,3) в Kotlin отличается от intArrayOf(1,2,3)?
A: `arrayOf(1,2,3)` и `intArrayOf(1,2,3)` отличаются типом массива и тем, как хранятся элементы.
	
**`arrayOf(1,2,3)`** создаёт объект `Array<Int>`
	
Особенности:
	
- использует объектный тип `Int`;
    
- элементы хранятся как объекты (boxed значения);
    
- работает с любыми типами (`Array<T>` — обобщённый массив).
    
---
**`intArrayOf(1,2,3)`** создаёт объект `IntArray`
	
Особенности:
	
- использует примитивный `int` под капотом JVM;
    
- без boxing/unboxing;
    
- меньше памяти и выше производительность;
    
- специализирован только для `Int`.
    
---
Пример несовместимости:
	
```kotlin
val a: Array<Int> = arrayOf(1,2,3)   // OK
val b: IntArray = intArrayOf(1,2,3)  // OK
	
// Ошибка:
val c: Array<Int> = intArrayOf(1,2,3)
```
	
Практическое правило:
	
- если нужна производительность и работа с числами → `IntArray`;
    
- если нужен универсальный массив (`Array<T>`, дженерики, смешанные операции) → `Array<Int>`.
    
Аналогично существуют:
	
```kotlin
doubleArrayOf()
longArrayOf()
booleanArrayOf()
charArrayOf()
```
<!--ID: 1778782722662-->


Q: Строки
A: То же самое, что и в Java.
	
Дополнительно:
	
Интерполяция - вставка данных в строку:
	
```kotlin
val name="John"
	
println("Hello $name")
println("2+2=${2+2}")
```
	
Многострочные:
	
```kotlin
val text="""
Hello
World
"""
```
<!--ID: 1778782722666-->


Q: Функции. Описание, типы, аргументы
A:   
	
```kotlin
fun sum(a:Int,b:Int):Int{
    return a+b
}
```
	
> Для функций с телом в `{}` тип НУЖНО указывать явно
	
---
	
Короткая форма (тут не обязательно указывать возвращаемый тип):
	
```kotlin
fun sum(a:Int,b:Int)=a+b
```
	
Аргументы по умолчанию:
	
```kotlin
fun hello(name:String="Guest")
```
	
Именованные параметры (передача аргументтов по имени):
	
```kotlin
hello(name="John")
```
<!--ID: 1778782822851-->


Q: Какие бывают функции в Kotlin?  
A:   
**1. Обычные (top-level) функции**
	
```kotlin
fun sum(a: Int, b: Int) = a + b
```
	
Могут существовать вне классов.
	
**2. Методы класса**
	
```kotlin
class User {
    fun printName() {}
}
```
	
Принадлежат экземпляру класса.
	
**3. Extension-функции**
	
```kotlin
fun String.lastChar() = this[length - 1]
```
	
Добавляют поведение существующему типу без изменения его кода.
	
**4. Локальные функции**
	
```kotlin
fun process() {
    fun validate() {}
}
```
	
Объявляются внутри другой функции.
	
**5. Анонимные функции**
	
```kotlin
val f = fun(x: Int): Int {
    return x * 2
}
```
	
Функция без имени.
	
**6. Лямбда-функции**
	
```kotlin
val f = { x: Int -> x * 2 }
```
	
Короткая форма анонимных функций.
	
**7. Inline-функции**
	
```kotlin
inline fun execute(action: () -> Unit) {
    action()
}
```
	
Тело функции подставляется в место вызова.
	
**8. Suspend-функции**
	
```kotlin
suspend fun loadData() {}
```
	
Используются в корутинах.
	
**9. Инфиксные функции**
	
```kotlin
infix fun Int.plusTen(x: Int) = this + x
	
5 plusTen 10
```
	
Вызываются без точки и скобок.
	
**10. Операторные функции**
	
```kotlin
operator fun plus(other: User): User
```
	
Перегружают операторы (`+`, `[]`, `()`, `in` и др.).
	
**11. Generic-функции**
	
```kotlin
fun <T> print(value: T) {}
```
	
Работают с параметрами типов.
	
**12. Higher-order функции**
	
```kotlin
fun execute(action: () -> Unit)
```
	
Принимают функции как параметры или возвращают их.
<!--ID: 1778782722671-->


Q: Лямбда-выражения и анонимные функции
A:   
Лямбда:
	
```kotlin
val sum = {a:Int,b:Int -> a+b}
```
	
Анонимная функция:
	
```kotlin
val sum = fun(a:Int,b:Int):Int{
    return a+b
}
```
<!--ID: 1778782722677-->


Q: Функции высокого порядка
A: Функции принимают другие функции.
	
```kotlin
fun execute(action: () -> Unit) {
    action()
}
```
	
Функции — это отдельный тип языка, а не только обёртка над функциональными интерфейсами.
	
`action: () -> Unit` — это не аналог `Runnable` или `Supplier<Void>`, а специальный функциональный тип:
	
```kotlin
() -> Unit
```
	
где:
	
- `()` — список аргументов;
    
- `Unit` — возвращаемый тип.
    
Есть разные функциональные типы:
	
```kotlin
(Int) -> String
(String, Int) -> Boolean
() -> Unit
(Int, Int) -> Int
```
	
Вызов всегда одинаковый (через `()`):
	
```kotlin
action()
operation(5)
mapper(10, 20)
```
	
В отличие от Java, здесь нет разделения на:
	
```java
Supplier<T> -> get()
Consumer<T> -> accept()
Function<T,R> -> apply()
Predicate<T> -> test()
Runnable -> run()
```
	
В Kotlin нет отдельных методов (`apply()`, `get()`, `accept()`). Есть единый синтаксис вызова через `()`.
<!--ID: 1778782722682-->


Q: Встроенные функции
A: Встроенные функции — это функции, уже входящие в стандартную библиотеку Kotlin и доступные без дополнительной реализации. Они покрывают типовые операции: работу с коллекциями (`map()`, `filter()`), строками (`substring()`), проверками (`let()`, `also()`) и другими часто используемыми задачами.
	
Часто используются:
	
```kotlin
listOf(1,2,3)
	
println()
	
maxOf(1,2)
	
repeat(3){
    println("Hi")
}
```
	
Коллекции:
	
```kotlin
list.map{}
list.filter{}
list.forEach{}
```
<!--ID: 1778782722687-->



Q: Инфиксная нотация функций
A: Позволяет вызывать функцию без точки и скобок.
	
```kotlin
infix fun Int.add(x:Int)=this+x
	
val result = 5 add 3
```
	
Требования:
	
- функция класса/extension
    
- один аргумент
<!--ID: 1778782722693-->
