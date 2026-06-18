
11-02-2026 15:16

Status:

Tags: [[Stream API]] [[Java+]]

---
# Method references - Stream API


### Reference to a static method

Тут всё и так понятно:
```java
//Function<T, R>
Function<String, Integer> = Integer::parseInt;
```


---
### Reference to an instance method of particular object

- Bound - привязывается к объекту в момент создания ссылки:
```java
StringBuilder sb = new StringBuilder("initial-string-");
Function<Integer, StringBuilder> appendFunc = sb::append;

 appendFunc.apply(5);
 sb.toString(); //initial-string-5
```

- Unbound - привязывается к объекту непосредственно в момент выполнения (передаём объект как аргумент)
```java
BiFunction<StringBuilder, Integer, StringBuilder> appendFunc = StringBuilder::append;

StringBuilder sb = new StringBuilder("initial-string-");
appendFunc.apply(sb, 5);
sb.toString(); //initial-string-5
```


---
### Reference to a constructor

Тут тоже всё просто:

```java
Supplier<List<String>> createListFunc = LinkedList::new;

List<String> list = createListFunc.get();
```

Если в конструктор надо передать параметры:

```java
static class MyObject {  
    MyObject(int a){  
        System.out.println(a);  
    }  
      
    MyObject(){}  
      
    MyObject(String s){}  
}

//ссылка:
Function<Integer, MyObject> myObjectCreator = MyObject::new;
myObjectCreator.apply(5);
```

----
#### [[Method references - Stream API - Flashcards|Link to flashcards]]



---
### References:

