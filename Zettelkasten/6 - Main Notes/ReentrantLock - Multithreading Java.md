
21-01-2026 20:16

Status: 

Tags: [[Multithreading]] [[Java+]]

---
# ReentrantLock - Multithreading Java

Гибкая альтернатива synchronized

|Возможность|`synchronized`|`ReentrantLock`|
|---|---|---|
|**Прерываемая блокировка**|❌|✅ `lockInterruptibly()`|
|**Таймауты**|❌|✅ `tryLock(timeout)`|
|**Условные переменные**|`wait()/notify()`|✅ `Condition` (гибче)|
|**Справедливость**|❌ (не гарантирована)|✅ `new ReentrantLock(true)`|
|**Проверка состояния**|❌|✅ `isLocked()`, `getHoldCount()`|
|**Non-blocking попытка**|❌|✅ `tryLock()`|

#### Основной ЖЦ

```java
ReentrantLock lock = new ReentrantLock();

lock.lock();  // Захватывает блокировку (или ждёт)
try {
    // Критическая секция
} finally {
    lock.unlock();  // ОБЯЗАТЕЛЬНО в finally!
}
```

##### Возможность reentrance в действии

(как и synchronized)

```java
ReentrantLock lock = new ReentrantLock();

public void outer() {
    lock.lock();  // holdCount=1
    try {
        inner();    // тот же поток может войти снова!
    } finally {
        lock.unlock();  // holdCount=0
    }
}

public void inner() {
    lock.lock();  // holdCount=2 (не блокируется!)
    try {
        // внутренняя критическая секция
    } finally {
        lock.unlock();  // holdCount=1
    }
}
```


### Полноценный пример

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


---
### `tryLock()`


- Пытается захватить блокировку **немедленно** или с таймаутом (если использовать перегрузку с таймаутом).
- Если блокировка **свободна** → возвращает `true` и захватывает её.
- Если блокировка **занята** → возвращает `false` и не блокирует поток.
- **Не блокирует поток**, что делает его удобным для "неблокирующих" операций.

### **Use cases:**

1. **Попытка работы с ресурсом без ожидания**
    
```java
var lock = ReentrantLock();

if (lock.tryLock()) {    
	try {        
	
	// работа с ресурсом    
	} finally {        
		lock.unlock()    
	}
} else {    
	println("Ресурс занят, пропускаем работу");
}
```
    
2. **Попытка с таймаутом**, полезно для ограничения ожидания:
    
```java
if (lock.tryLock(500, TimeUnit.MILLISECONDS)) {
    try { 
	    /* работа */ 
	} finally { 
		lock.unlock();
	}
} else {    
	println("Не удалось получить блокировку за 500 мс");
}
```


---
### `lockInterruptibly()`


- Захватывает блокировку **или ожидает её освобождения**, **но может быть прерван потоком** через `Thread.interrupt()`.
- Если поток прерван **~={red}во время ожидания=~** → выбрасывается `InterruptedException`.
- Используется, когда нужно **не зависать навсегда**, а реагировать на прерывания.

`lock()` **не реагирует на прерывание, пока поток ждет захвата замка**. Поток будет продолжать ждать, а `interrupt()` не выведет его из ожидания.

`lockInterruptibly()` **можно прервать во время ожидания**: если поток ждет lock и его прервали, он сразу завершит ожидание и выбросит `InterruptedException`.

##### Кратко

- `lock()` — ждет до получения lock, прерывание не освобождает от ожидания.
    
- `lockInterruptibly()` — ждет lock, но может выйти по `interrupt()` с `InterruptedException`

### **Use cases:**

1. **Работа с долгими блокировками, с возможностью отмены**
    
```java
var lock = ReentrantLock();

try {
    lock.lockInterruptibly();  // можно прервать извне
    try {
        // долгий процесс
    } finally {
        lock.unlock();
    }
} catch (e: InterruptedException) {
    println("Операция прервана");
}
```


----
#### [[ReentrantLock - Multithreading Java - Flashcards|Link to flashcards]]



---
### References:

