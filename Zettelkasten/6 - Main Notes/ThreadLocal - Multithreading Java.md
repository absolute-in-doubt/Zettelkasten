
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


---
### ~={orange}Какие могут быть проблемы при использовании ThreadLocal в реактивных системах?=~


Главная проблема: **`ThreadLocal` привязан к потоку, а в реактивном программировании выполнение одной операции может переходить между разными потоками.**

Например, в WebFlux/Reactor:

```java
String userId = ...;
threadLocal.set(userId);

return Mono.just("data")
    .publishOn(Schedulers.boundedElastic())
    .map(x -> threadLocal.get()); // может быть null
```

### Основные проблемы

- **Потеря контекста** — `ThreadLocal.get()` может вернуть `null`, потому что continuation (продолжение - другая лямбда, выполняется на другом потоке) выполняется на другом потоке.
    
- **Утечка контекста между запросами** — если забыть `remove()`, значение может остаться в worker-потоке и потенциально быть увидено следующей задачей.
    
- **Непредсказуемость при thread hopping** — `publishOn`, `subscribeOn`, `flatMap`, scheduler'ы и т.д. могут менять поток выполнения.
    
- **Сложно отлаживать** — поведение зависит от scheduler'ов и конкретного места переключения потоков.
    
- **Нарушение изоляции request context** — нельзя считать, что `ThreadLocal` автоматически соответствует текущему HTTP-запросу.


### Что использовать вместо этого

В Reactor обычно используют **Reactor Context**:

```java
return Mono.just("data")
    .contextWrite(ctx -> ctx.put("userId", userId))
    .flatMap(x ->
        Mono.deferContextual(ctx -> {
            String userId = ctx.get("userId");
            return ...;
        })
    );
```

`Context` привязан не к `Thread`, а к **reactive execution/subscription**.


> `ThreadLocal` плохо подходит для reactive, потому что reactive pipeline может переключаться между потоками. Значение ThreadLocal при этом теряется или может случайно протечь в другой execution. В Reactor для request context обычно используют Reactor Context.



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

