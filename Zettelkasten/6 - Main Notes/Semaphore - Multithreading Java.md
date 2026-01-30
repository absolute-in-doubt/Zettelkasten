
22-01-2026 09:11

Status: #adult 

Tags: [[Multithreading]] [[Java+]]

---
# Semaphore - Multithreading Java

`java.util.concurrent.Semaphore`

`Semaphore` в Java  — **счётчик разрешений**, позволяющий **ограничить количество потоков**, одновременно работающих с ресурсом. В отличие от `Lock` / `synchronized`, семафор **не привязан к владельцу** — любой поток может `release()` разрешения.

```java
Semaphore semaphore = new Semaphore(3); // 3 разрешения

semaphore.acquire();  // ждёт разрешение, permits--
try {
    // Критическая секция
} finally {
    semaphore.release(); // permits++
}
```


### Продвинутые возможности

```java
Semaphore semaphore = new Semaphore(3, true); // fair=true (FIFO)

// Неблокирующая попытка
if (semaphore.tryAcquire()) {
    // Получил разрешение
} else {
    // Нет разрешения, продолжаем без него
}

// Таймаут
if (semaphore.tryAcquire(1, TimeUnit.SECONDS)) {
    // Получил за 1 сек
}

// Массовое освобождение
semaphore.release(2); // +2 разрешения
semaphore.drainPermits(); // забирает все разрешения

// Мониторинг
System.out.println("Доступно: " + semaphore.availablePermits());
System.out.println("В очереди: " + semaphore.getQueueLength());
```



### Практический пример

(пул соединений с БД)

```java
import java.util.concurrent.Semaphore;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class DatabaseConnectionPool {
    private final Semaphore availableConnections = new Semaphore(3, true); // fair=true

    public void executeQuery(String sql) {
        try {
            // Ждём свободное соединение
            availableConnections.acquire();
            System.out.println(Thread.currentThread().getName() 
                + " получил соединение. Permits: " + availableConnections.availablePermits());
            
            // Имитация запроса к БД
            Thread.sleep(2000);
            
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            // Возвращаем соединение в пул
            availableConnections.release();
            System.out.println(Thread.currentThread().getName() 
                + " вернул соединение. Permits: " + availableConnections.availablePermits());
        }
    }

    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(10);
        DatabaseConnectionPool pool = new DatabaseConnectionPool();

        // 10 потоков борются за 3 соединения
        for (int i = 0; i < 20; i++) {
            executor.submit(() -> pool.executeQuery("SELECT * FROM users"));
        }
        
        executor.shutdown();
    }
}
```

```
pool-1 получил соединение. Permits: 2
pool-2 получил соединение. Permits: 1  
pool-3 получил соединение. Permits: 0
pool-4 ждёт...
pool-1 вернул соединение. Permits: 1
pool-4 получил соединение. Permits: 0
...
```




----
#### [[Semaphore - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

