
Theory for the cards: [[CountDownLatch & CyclicBarrier - Multithreading Java]]

FILE TAGS: multithreading java_interview

Q: Как работает CountDownLatch? (опиши с названиями методов) И как создать CountDownLatch?
A: `CountDownLatch` используется для синхронизации потоков. Поток, остановленный методом `latch.await()` возобновляется только когда counter этого `CountDownLatch` становится равным нулю. Остальные же потоки уменьшают counter вызывая `latch.countDown()`.
	
> `CountDownLatch` одноразовый
	
Создание:  (в конструктор передаём изначальное значение counter)
	
```java
new CountDownLatch(workers);
```
	
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
<!--ID: 1769092931458-->



Q: Как работает CyclicBarrier? (Опиши с названиями методов) И как создать CyclicBarrier?
A: CyclicBarrier используется для синхронизации нескольких потоков в одной точке.
	
Инициализируется количеством потоков, которые должны «встретиться» в точке барьера:
	
```java
new CyclicBarrier(amtOfThreads,
                () -> System.out.println("Все дошли до барьера, продолжаем"));
```
	
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
<!--ID: 1769092931464-->

