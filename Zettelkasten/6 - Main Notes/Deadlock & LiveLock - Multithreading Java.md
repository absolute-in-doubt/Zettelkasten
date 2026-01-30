
21-01-2026 20:29

Status:

Tags: [[Multithreading]] [[Java+]]

---
# Deadlock $ LiveLock - Multithreading Java


#### Deadlock (взаимная блокировка)

Потоки **навсегда блокируют друг друга**, каждый ждёт ресурс, удерживаемый другим.  
Итог: система **стоит**, прогресса нет.

**Пример:**  

- Поток A держит ресурс 1 и ждёт ресурс 2.  
- Поток B держит ресурс 2 и ждёт ресурс 1.


**Как избегать:**

1. **Всегда блокировать ресурсы в одном порядке**.
    
2. Использовать **таймауты при захвате блокировок** (`tryLock(timeout)`).
    
3. Минимизировать количество одновременно захватываемых блокировок.


#### Livelock (ожившая блокировка)

Потоки **не заблокированы**, но **бесполезно двигаются**, постоянно пытаясь избежать конфликта и мешая друг другу.  
Итог: система работает, но **прогресса тоже нет**.

**Пример:**  
Два потока уступают друг другу ресурс, отказываются и пытаются снова, но синхронно, и бесконечно.

**Как избегать:**

1. Добавлять **рандомные задержки** или **экспоненциальный бэкофф** при повторных попытках.
    
2. Использовать **явные таймауты** и прекращать попытки через определённое время.
    
3. Пересматривать **алгоритм кооперации**, чтобы не блокировать друг друга непрерывно.

**Пример:**
1. Поток А заблокировал ресурс 1, пытается взять ресурс 2.
   Поток В заблокировал ресурс 2, пытается взять ресурс 1.

2. Поток А не может вязть ресурс 1 (занят потоком В в момент проверки ) -> освобождает ресурс 1.
   Поток В не может вязть ресурс 2 (занят потоком А в момент проверки) -> освобождает ресурс 1.

3. Потоки одновременно одновременно (без random timeout) заново начинают выполнение, блокируя ресурсы и возвращаясь на шаг 1.

### Пример LiveLock 

```java
import java.util.concurrent.locks.ReentrantLock;
import java.util.Random;

public class LivelockExample {
    private static final ReentrantLock fork1 = new ReentrantLock();
    private static final ReentrantLock fork2 = new ReentrantLock();

    static class Philosopher implements Runnable {
        private final String name;
        private final ReentrantLock leftFork, rightFork;

        Philosopher(String name, ReentrantLock leftFork, ReentrantLock rightFork) {
            this.name = name;
            this.leftFork = leftFork;
            this.rightFork = rightFork;
        }

        @Override
        public void run() {
            Random rand = new Random();
            while (true) {
                // Попытка взять левую вилку
                if (leftFork.tryLock()) {
                    System.out.printf("%s взял левую вилку%n", name);
                    Thread.sleep(rand.nextInt(100)); // "думает"

                    // "Вежливо" проверяет правую вилку
                    if (!rightFork.tryLock()) {
                        // Вежливо отдаёт левую вилку!
                        leftFork.unlock();
                        System.out.printf("%s ВЕЖЛИВО отдал левую вилку%n", name);
                        
                        //тут бы случайную паузу въебать 
                        //для предотвращения LiveLock
                    }

                    // Успех! Ест
                    System.out.printf("%s ЕСТ!%n", name);
                    try { Thread.sleep(1000); } catch (InterruptedException e) {}

                    rightFork.unlock();
                    leftFork.unlock();
                }
            }
        }
    }

    public static void main(String[] args) {
        new Thread(new Philosopher("Сократ", fork1, fork2)).start();
        new Thread(new Philosopher("Платон", fork2, fork1)).start();
    }
}

```

Output:
```
Сократ взял левую вилку
Платон взял левую вилку  
Сократ ВЕЖЛИВО отдал левую вилку
Платон ВЕЖЛИВО отдал левую вилку
Сократ взял левую вилку
Платон взял левую вилку
Сократ ВЕЖЛИВО отдал левую вилку
Платон ВЕЖЛИВО отдал левую вилку
... **БЕСКОНЕЧНО!** 🚀🚀🚀
```


----
#### [[Deadlock & LiveLock - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

