
19-09-2026 12:27

Status:

Tags: [[jcmd - JVM Diagnostics & Monitoring]] [[Multithreading]]

---
# Обнаружение Deadlocks через Thread Dump


```java
public class DeadlockDemo {  
    private static final Object LOCK_1 = new Object();  
    private static final Object LOCK_2 = new Object();  
  
    public static void main(String[] args) {  
  
        Thread t1 = new Thread(() -> {  
            synchronized(LOCK_1) {  
                try {  
                    Thread.sleep(100);  
                } catch(InterruptedException e){  
  
                }  
  
                synchronized (LOCK_2){  
                    System.out.println(Thread.currentThread().getName() + " acquired both locks");  
                }  
            }  
        });  
  
        Thread t2 = new Thread(() -> {  
            synchronized(LOCK_2) {  
                try {  
                    Thread.sleep(100);  
                } catch(InterruptedException e){  
  
                }  
  
                synchronized (LOCK_1){  
                    System.out.println(Thread.currentThread().getName() + " acquired both locks");  
                }  
            }  
        });  
  
        t1.start();  
        t2.start();  
    }  
}
```


### 1. По логам определяем, что программа зависла

(тут логов нет)


### 2. Берем дамп потока

Для этого сначала нужно узнать PID потока:

```bash
PS C:\java-workspace\deadlock-dump-demo> jps
35520 Launcher
36816 RemoteMavenServer36
37616 Jps
18984 Launcher
34488 DeadlockDemo # Наш процесс
3004 Main
```

После этого есть несколько вариантов:

1. `jcmd 34488 Thread.print > thread_dump#1.txt`

2. `jstack 34488 > thread_dump#2.txt`

Дают идентичные результаты. Но рекомендуется использовать `jcmd` как более современный.


### 3. Анализируем дамп и ищем циклы

Часть thread dump:

```
Found one Java-level deadlock:  
=============================  
"Thread-0":  
  waiting to lock monitor 0x000002ab926ac860 (object 0x000000071bef1de8, a java.lang.Object),  
  which is held by "Thread-1"  
  
"Thread-1":  
  waiting to lock monitor 0x000002ab926ac930 (object 0x000000071bef1dd8, a java.lang.Object),  
  which is held by "Thread-0"  
  
Java stack information for the threads listed above:  
===================================================  
"Thread-0":  
    at org.example.DeadlockDemo.lambda$main$0(DeadlockDemo.java:18)  
    - waiting to lock <0x000000071bef1de8> (a java.lang.Object)  
    - locked <0x000000071bef1dd8> (a java.lang.Object)  
    at org.example.DeadlockDemo$$Lambda/0x000002abb6003000.run(Unknown Source)  
    at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)  
    at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)  
"Thread-1":  
    at org.example.DeadlockDemo.lambda$main$1(DeadlockDemo.java:32)  
    - waiting to lock <0x000000071bef1dd8> (a java.lang.Object)  
    - locked <0x000000071bef1de8> (a java.lang.Object)  
    at org.example.DeadlockDemo$$Lambda/0x000002abb6003220.run(Unknown Source)  
    at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)  
    at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)  
  
Found 1 deadlock.
```
Тут стоит смотреть на коды монитор object-ов. Например здесь видно, что Thread-1:
- locked <0x000000071bef1dd8>
- waiting to lock <0x000000071bef1de8>

А Thread-2:
- waiting to lock <0x000000071bef1dd8>
- locked <0x000000071bef1de8>

Соответственно это deadlock с двумя локами.


### 4. Определяем, где это произошло в исходном коде

```bash
Java stack information for the threads listed above:  
===================================================  
"Thread-0":  
	# тут есть ссылки на исходный код
	# DeadlockDemo.java:18
    at org.example.DeadlockDemo.lambda$main$0(DeadlockDemo.java:18)  
    - waiting to lock <0x000000071bef1de8> (a java.lang.Object)  
    - locked <0x000000071bef1dd8> (a java.lang.Object)  
    at org.example.DeadlockDemo$$Lambda/0x000002abb6003000.run(Unknown Source)  
    at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)  
    at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)  
"Thread-1":  
	# DeadlockDemo.java:32
    at org.example.DeadlockDemo.lambda$main$1(DeadlockDemo.java:32)  
    - waiting to lock <0x000000071bef1dd8> (a java.lang.Object)  
    - locked <0x000000071bef1de8> (a java.lang.Object)  
    at org.example.DeadlockDemo$$Lambda/0x000002abb6003220.run(Unknown Source)  
    at java.lang.Thread.runWith(java.base@23.0.1/Thread.java:1588)  
    at java.lang.Thread.run(java.base@23.0.1/Thread.java:1575)  
```

Таким образом, понимаем, что в исходном коде потоки ждут в deadlock в:
- DeadlockDemo.java:18
- DeadlockDemo.java:32

### 5. Apply the fix

- Сделать захват в одном и том же порядке + добавить таймауты

- Использовать `ReentrantLock` и его `tryLock()` метод 

----
#### [[Обнаружение Deadlocks через Thread Dump - Flashcards|Link to flashcards]]



---
### References:

- [[Deadlock & LiveLock - Multithreading Java]]