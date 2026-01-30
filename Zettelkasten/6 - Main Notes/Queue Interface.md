
02-09-2025 16:43

Status: #child 

Tags: [[Java Core]] [[Java Collections]]

---
# Queue Interface

## FIFO 

#### Implementations:
- LinkedList
- PriorityQueue
- ArrayDeque
- ConcurrentLinkedList

#### + еще поддерживающие blocking operations:
- ArrayBlockingQueue
- LinkedBlockingQueue
- PriorityBlockingQueue
Методы для блокировки очереди -> java.util.concurrent.BlockingQueue

> [!note]
> Только blocking-очереди могут иметь фиксированный размер



### Operations:

Queue помимо методов из Collection предлагает по два метода на операции вставки, чтения и удаления: один выкидывает исключение, а другой - нет.

| Type of operation                           | Throws exception | Returns special value | The value |
| ------------------------------------------- | ---------------- | --------------------- | --------- |
| вставка                                     | .add(e)          | offer(e)              | false     |
| remove from the head (и получение значения) | remove()         | poll()                | null      |
| return the head but don't remove            | element()        | peek()                | null      |

>[!note]
.offer(e) возвратит null, если Размер очереди уже равен максимальной (только в блокируемых очередях) и он не сможет вставить новое значение.



### `equals`

~={orange}Реализации и `Deque`, и `Queue` обычно не переопределяют методы `equals()` и `hashCode()`, вместо этого используются унаследованные методы класса Object, основанные на сравнении ссылок.=~

У очередей **нет чёткого определения "логического равенства"**:

```java
Queue<Integer> q1 = new LinkedList<>();
q1.add(1); q1.add(2); q1.add(3);  // [1,2,3]

Queue<Integer> q2 = new LinkedList<>();
q2.add(2); q2.add(3); q2.add(1);  // [2,3,1]
```

**Вопрос:** `q1.equals(q2)` должно вернуть `true` или `false`?

- **По содержимому** — одинаковые элементы `{1,2,3}`
    
- **По порядку** — разные последовательности



----
#### [[Queue Interface Flashcards|Link to flashcards]]



---
### References:

