 
23-09-2025 16:32

Status: 

Tags: [[Java Core]]

---
# Functional Interfaces - Java

- Функциональный интерфейс — это интерфейс, у которого есть **ровно один абстрактный метод (SAM — Single Abstract Method)**.
    
- ~={red}!!!=~ ~={yellow}Допускается наличие `default` и `static` методов, но **одного** абстрактного метода.=~ ~={red}!!!=~

>[!note]
>Аннотация @FunctionalInterface - проверяет на этапе компиляции только то, что интерфейс имеет один метод



### Как работает 

```java
@FunctionalInterface  
public interface TripleFunction<T, U, N, R> {  
    R apply(T t, U u, N n);  
}
...

TripleFunction<Integer, Integer, Integer, Integer> sum = (a, b, c) -> a + b + c;
System.out.println(sum.apply(1, 2, 3)); // 6
```

➡️ На уровне байткода это превращается в:

- Генерацию `invokedynamic` инструкции.
	
- Системный вызов `LambdaMetafactory.metafactory(...)`.
	
- Создание прокси-объекта, у которого метод `apply(...)` вызывает реализацию логики из лямбды: `(a, b, c) -> a + b + c`.
	
- ~={cyan}возвращает invokedynamic конструкцию=~ ~={red}->=~ ~={yellow} sum теперь указывает на созданную `invokedynamic` конструкцию (аналог класса, реализующего интерфейс `TripleFunction<T, U, N, R>`)=~  ~={red}!!!=~


Т.е. происходит примерно вот что:
```java
class SyntheticTripleFunction implements TripleFunction<Integer, Integer, Integer, Integer> {
    @Override
    public Integer apply(Integer t, Integer u, Integer n) {
        // делегирование к телу лямбды
        return t + u + n;
    }
}
```

> [!warning] **SyntheticTripleFunction НЕ создаётся буквально**
> Скорее это набор инструкций, который вшивается в байткод.
> Т.е. в runtime делается одноразовый объект, у которого есть функция apply, в тело которой подставляется лямбда 
>
> -или метод, как тут например: 
> ```java
> class MyClass {
>   static int addTriple(int a, int b, int c){
> 	 return a+b+c;
> };
> ...
>TripleFunction<Integer, Integer, Integer, Integer> sum = MyClass::add;
System.out.println(sum.apply(1, 2, 3));
> ````


#### Поэтапно

Лямбда **не является объектом сама по себе** — это синтаксический сахар для реализации функционального интерфейса.

Компилятор **не генерирует отдельный анонимный класс для самой лямбды**, как для старых anonymous classes.


- В байткоде используется инструкция **`invokedynamic`**, которая при первом выполнении связывается с bootstrap-методом `LambdaMetafactory`.

- JVM создаёт/получает экземпляр нужного функционального интерфейса:
    
    ```java
    Consumer<Integer> c = x -> System.out.println(x);
    ```
    
- Если лямбда **не захватывает переменные**, JVM часто может переиспользовать один экземпляр:
    
    ```java
    () -> doSomething()
    ```
    
- Если лямбда **захватывает состояние**, создаётся экземпляр, содержащий это состояние:
    
    ```java
    int n = 10;
    x -> x + n
    ```
    
- После JIT-прогрева вызов лямбды может быть **inlined**, и overhead самого функционального вызова часто практически исчезает.

----
#### [[Functional Interfaces - Java - Flashcards|Link to flashcards]]



---
### References:

