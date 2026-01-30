
Theory for the cards: [[Excutor & ExecutorService - Multithreading Java]]

FILE TAGS: java_interview multithreading

Q: Чем Executor отличается от ExecutorService?
A: Executor - интерфейс определяющий один метод `execute()`, т.е. его реализации могут как угодно выполнять получаемый Runnable.
	
Пример:
```java
Executor direct = command -> command.run();      // выполняет в текущем потоке
Executor async  = r -> new Thread(r).start();   // каждый раз новый поток
```
	
ExecutorService - расширяет Executor и добавляет:
	
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
<!--ID: 1769065537738-->


Q: Какие ты знаешь реализации ExecutorService и чем они отличаются?
A:   
	
| Executor Type            | Description                                                                                              | Syntax Example                                                            |
| ------------------------ | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **SingleThreadExecutor** | Creates a thread pool with a single thread that executes tasks sequentially.                             | ExecutorService executor = Executors.newSingleThreadExecutor();           |
| **FixedThreadPool**      | Creates a pool with a fixed number of threads. Excess tasks are queued until a thread becomes available. | ExecutorService pool = Executors.newFixedThreadPool(2);                   |
| **CachedThreadPool**     | Creates threads as needed and reuses idle ones. Suitable for many short-lived asynchronous tasks.        | ExecutorService pool = Executors.newCachedThreadPool();                   |
| **ScheduledThreadPool**  | Executes tasks periodically or after a specified delay.                                                  | ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1); |
<!--ID: 1769065537746-->

