
07-05-2026 15:15

Status:

Tags: [[Multithreading]] [[Java+]]

---
# ThreadLocal - Multithreading Java



**ThreadLocal** в Java — обертка для хранения **thread-local переменных**: каждый поток видит **свою изолированную копию** значения, без синхронизации.

Даёт возможность создавать static переменные, которые будут хранить значение уникальное для этого потока, таким образом, если класс используется в двух потоках, в этой static переменной будет свое значение для каждого потока и не будет race conditions.


- **Внутренняя реализация**: `ThreadLocalMap` в `Thread` (weak keys — ThreadLocal instances). `get()/set()` — O(1) доступ.
    
- **Жизненный цикл**:
    
    - `set(T value)` — устанавливает для текущего потока.
        
    - `get()` — возвращает (null, если не set).
        
    - `remove()` — **обязательно очищать** (избежать memory leak в пулах потоков).
        
    - `withInitial(Supplier)` — дефолтное значение.
        
- **InheritableThreadLocal**: Наследуется дочерними потоками (`child.initValue()`).
	
- **Проблемы**: 
	- Memory leaks в ThreadPool (Tomcat/Executor), если не `remove()`. 
		
	- WeakRef не спасает от strong value refs.


### Как обычно делают

Обычно задаём `ThreadLocal` переменные в корне потока как `static` переменные. Таким образом они доступны везде в потоке:

```java
public class MyRunnable implements Runnable {
    private static final ThreadLocal<String> CTX = ThreadLocal.withInitial(() -> "default");

    @Override
    public void run() {  // Корень потока
        CTX.set("task-started");  // Инициализация здесь
        method1();  // Видно дальше
    }

    void method1() {
        System.out.println(CTX.get());  // "task-started" — видно!
        method2();
    }
    void method2() { CTX.get(); }  // Работает
}

// Запуск
new Thread(new MyRunnable()).start();  // CTX виден во всём run()
```



----
## Реальные use cases (упрощённо)


#####  1. **User Context в Web** (Spring Security)

```java
public class UserContext {
    private static final ThreadLocal<String> USER_ID = new ThreadLocal<>();
    
    public static void setUser(String id) { USER_ID.set(id); }
    public static String getUser() { return USER_ID.get(); }
    public static void clear() { USER_ID.remove(); }
}

// В Filter/Interceptor
UserContext.setUser(request.getUserId());
try { 
	chain.doFilter(); 
} finally { 
	UserContext.clear(); 
}  // Используется везде в потоке
```

Избегает передачи user по всем методам.

## 2. **SimpleDateFormat** (не thread-safe)

```java
private static final ThreadLocal<SimpleDateFormat> DATE_FORMAT = 
    ThreadLocal.withInitial(() -> new SimpleDateFormat("dd.MM.yyyy"));

public String format(Date date) {
    return DATE_FORMAT.get().format(date);  // Каждый поток — свой SDF
}
```

Без locks/global sharing.

## 3. **Transaction ID / Logging Context** (MDC-like)

```java
private static final ThreadLocal<String> TX_ID = new ThreadLocal<>();
// В начале транзакции: 
TX_ID.set(UUID.randomUUID().toString());
// Log4j/SLF4J берёт из ThreadLocal для trace ID
```


**Когда избегать**: Не для immutable/простых типов (лучше параметры). В reactive/CompletableFuture — не работает (виртуальные потоки).

----
#### [[ThreadLocal - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

