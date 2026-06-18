
22-01-2026 09:41

Status: #child 

Tags: [[Multithreading]] [[Java+]]

---
# Executor & ExecutorService - Multithreading Java

![[Pasted image 20260122094137.png]]


`Executor` и `ExecutorService` — часть **Executor Framework** (`java.util.concurrent`), который отделяет _что делать_ (задача `Runnable`/`Callable`) от _как выполнять_ (пулы потоков, очереди и т.д.)

### Executor

 Интерфейс с **одним методом**:

```java
public interface Executor {
    void execute(Runnable command);
}
```


Идея: дать абстракцию «исполнителя задач» вместо прямой работы с `new Thread(...)`. Конкретная реализация сама решает, в каком потоке и когда выполнять `Runnable`: немедленно, в пуле потоков, отложенно и т.п.

Пример минимальной реализации:

```java
Executor direct = command -> command.run();      // выполняет в текущем потоке
Executor async  = r -> new Thread(r).start();   // каждый раз новый поток
```


---
### ExecutorService

`ExecutorService` — **надстройка над Executor**, которая добавляет:

- управление жизненным циклом (shutdown);
    
- отправку задач с получением результата через `Future`;
    
- групповые операции `invokeAll`, `invokeAny`.

```java
public interface ExecutorService extends Executor {
    void shutdown();
    List<Runnable> shutdownNow();
    <T> Future<T> submit(Callable<T> task);
    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks);
    boolean awaitTermination(long timeout, TimeUnit unit) throws InterruptedException;
    // и др.
}
```


---

| Executor Type            | Description                                                                                              | Syntax Example                                                            |
| ------------------------ | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **SingleThreadExecutor** | Creates a thread pool with a single thread that executes tasks sequentially.                             | ExecutorService executor = Executors.newSingleThreadExecutor();           |
| **FixedThreadPool**      | Creates a pool with a fixed number of threads. Excess tasks are queued until a thread becomes available. | ExecutorService pool = Executors.newFixedThreadPool(2);                   |
| **CachedThreadPool**     | Creates threads as needed and reuses idle ones. Suitable for many short-lived asynchronous tasks.        | ExecutorService pool = Executors.newCachedThreadPool();                   |
| **ScheduledThreadPool**  | Executes tasks periodically or after a specified delay.                                                  | ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1); |

----
#### [[Excutor & ExecutorService - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

