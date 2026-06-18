
Theory for the cards: [[ReentrantLock - Multithreading Java]]

FILE TAGS: multithreading

Q: Расскажи про ReentrantLock. Чем он отличается от synchronized? Приведи пример use case, в котором необходимо использовать ReentrantLock и использование synchronized не удобно/невозможно.
A:   
ReentrantLock + Condition** — единственный способ в Java реализовать эффективное ожидание условия без `busy-waiting` (бесконечных проверок). `synchronized` ограничен примитивным `wait/notify`.
	
	
```java
public class BoundedBuffer {
    private final ReentrantLock lock = new ReentrantLock();
    private final Condition notFull = lock.newCondition();
    private final Condition notEmpty = lock.newCondition();
    private final Object[] items = new Object[10];
    private int putIndex = 0, takeIndex = 0, count = 0;
	
    public void put(Object x) throws InterruptedException {
        lock.lock();
        try {
            // Ждём, пока не будет места
            while (count == items.length)
                notFull.await();
            
            items[putIndex] = x;
            if (++putIndex == items.length) putIndex = 0;
            ++count;
            
            // Уведомляем потребителей
            notEmpty.signal();
        } finally {
            lock.unlock();
        }
    }
	
    public Object take() throws InterruptedException {
        lock.lock();
        try {
            // Ждём, пока не будет элементов
            while (count == 0)
                notEmpty.await();
            
            Object x = items[takeIndex];
            if (++takeIndex == items.length) takeIndex = 0;
            --count;
            
            // Уведомляем продюсеров
            notFull.signal();
            return x;
        } finally {
            lock.unlock();
        }
    }
	
    // Non-blocking попытка
    public boolean tryPut(Object x, long timeout, TimeUnit unit) 
            throws InterruptedException {
        if (lock.tryLock(timeout, unit)) {
            try {
                if (count < items.length) {
                    // [логика вставки]
                    return true;
                }
            } finally {
                lock.unlock();
            }
        }
        return false;
    }
}
```
	
**Метод `tryLock()` в `ReentrantLock`** — это неблокирующая попытка захвата блокировки: возвращает `true` сразу при успехе или `false`, если блокировка уже занята другим потоком. В отличие от `lock()`, не заставляет поток ждать. 
	
**Варианты `tryLock()`**
	
- `tryLock()` — **немедленная попытка** (0 мс ожидания)
- `tryLock(long timeout, TimeUnit unit)` — **ожидание с таймаутом** (например, 5 секунд)
	
- Оба **не выбрасывают `InterruptedException`** (кроме версии с таймаутом)
	
**TL;DR(`tryLock()`):** Быстро попробовали захватить блокировк + выполнить логику. Не получилось - хуй с ним (ну или чуть чуть подождем, а там хуй с ним).
<!--ID: 1772015275040-->
