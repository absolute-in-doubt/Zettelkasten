
Theory for the cards: [[Queue Interface]]

FILE TAGS: java_core

Q: Как получить head-элемент очереди без его удаления?
A: .element() или .peek()
<!--ID: 1756822375899-->


Q: Как получить и удалить head-элемент очереди?
A: remove() или poll()
<!--ID: 1756822375912-->


Q: Как вставить элемент в начало очереди?
A: add(e) или offer(e)
<!--ID: 1756822375920-->


Q: Что вернет вызов метода .offer()?
```java
Queue<Character> q = new ArrayBlockingDeque<>(1);
q.add('a');
q.offer('b');
```
A: Он вернет false и `'b'` не будет вставлено в очередь.
<!--ID: 1756822375927-->


Q: Что вернет второй вызов метода .add()?
```java
Queue<Character> q = new ArrayBlockingDeque<>(1);
q.add('a');
q.add('b');
```
A: Он вызовет IllegalStateException
<!--ID: 1756822375934-->


Q: Какие очереди могут иметь фиксированный размер?
A: blocking-очереди (реализующие java.util.concurrent.BlockingQueue) 
<!--ID: 1756822375941-->

Q: Почему `Queue` и `Deque` не преопределяют методы `equals()` и `hashCode()`, а вместо этого используют методы класса `Object`, основанные на сравнении ссылок?
A: Потому что в `Queue` не понятно, считать ли равными две очереди содержащие одни и те же элементы но в разном порядке: 
	
```java
Queue<Integer> q1 = new LinkedList<>();
q1.add(1); q1.add(2); q1.add(3);  // [1,2,3]
	
Queue<Integer> q2 = new LinkedList<>();
q2.add(2); q2.add(3); q2.add(1);  // [2,3,1]
```
	
А `Deque` ко всему прочему и не специфицирует явно, как он должен использоваться: FIFO или LIFO.
<!--ID: 1768734370308-->
