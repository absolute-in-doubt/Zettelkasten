
21-01-2026 21:15

Status:

Tags: [[Multithreading]] [[Java+]]

---
# CountDownLatch & CyclicBarrier - Multithreading Java

## CountDownLatch

- Инициализируется счётчиком событий, после n вызовов `countDown()` все ожидающие потоки на `await()` продолжают работу.
    
- Одноразовый: после достижения нуля переиспользовать нельзя.

```java
class Worker implements Runnable {
    private final CountDownLatch latch;

    Worker(CountDownLatch latch) {
        this.latch = latch;
    }

    @Override
    public void run() {
        try {
            // какая-то работа
        } finally {
            latch.countDown(); // сигнал "я закончил"
        }
    }
}

public class DemoLatch {
    public static void main(String[] args) throws InterruptedException {
        int workers = 3;
        CountDownLatch latch = new CountDownLatch(workers);

        for (int i = 0; i < workers; i++) {
            new Thread(new Worker(latch)).start();
        }

        latch.await(); // ждём, пока все уменьшают счётчик до 0
        System.out.println("Все воркеры завершили работу");
    }
}
```



## CyclicBarrier

- Инициализируется количеством потоков, которые должны «встретиться» в точке барьера.
    
- Каждый поток вызывает `await()`, все блокируются, пока не соберутся `parties`, затем все разом идут дальше; после этого барьер можно использовать снова (циклический).

```java
public class DemoBarrier {
    public static void main(String[] args) {
        int parties = 3;

        CyclicBarrier barrier = new CyclicBarrier(parties,
                () -> System.out.println("Все дошли до барьера, продолжаем"));

        Runnable task = () -> {
            try {
                System.out.println(Thread.currentThread().getName() + " сделал шаг 1");
                barrier.await(); // синхронизация точки
                System.out.println(Thread.currentThread().getName() + " сделал шаг 2");
            } catch (Exception e) {
                Thread.currentThread().interrupt();
            }
        };

        for (int i = 0; i < parties; i++) {
            new Thread(task).start();
        }
    }
}

```

~={orange}**"несколько потоков идут шагами и должны периодически выравниваться по общей точке"**=~

----
#### [[CountDownLatch & CyclicBarrier - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

