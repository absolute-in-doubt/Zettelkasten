
Theory for the cards: [[Semaphore - Multithreading Java]]

FILE TAGS: multithreading java_interview

Q: Что такое Semaphore? Как он используется?
A: Semaphore используется для ограничения количества потоков, одновременно использующих ресурс.
	
```java
Semaphore semaphore = new Semaphore(3); // 3 разрешения
	
semaphore.acquire();  // ждёт разрешение, permits--
try {
    // Критическая секция
} finally {
    semaphore.release(); // permits++
}
```
<!--ID: 1769093147669-->
