
02-09-2025 10:28

Status: #child 

Tags: [[Java Core]] [[Java Collections]]

---
# Set Interface


## Реализации:

##### HashSet 
- best performing
- no order
- ~={orange}Быстрая=~ работа:~={cyan} O(1) =~на вставку, удаление, поиск (в среднем).
##### TreeSet
- Red-Black tree
- хранит все в отсортированном виде (строки - по алфавиту)
- Более ~={orange}медленный=~: ~={cyan}O(log n)=~ на вставку, удаление, поиск.
##### LinkedHashSet
- **сохраняет порядок вставки элементов**,
- работает на основе **`HashMap` + двусвязный список** для хранения порядка.
- ~={orange}Быстрая=~ работа:~={cyan} O(1) =~на вставку, удаление, поиск (в среднем).
-  **под капотом - LinkedHashMap**

>**~={red}?!=~** `HashSet` в Java обычно реализован **на базе `HashMap`**, где сам элемент Set хранится как ключ, а значение — это внутренняя заглушка.
>
>Эта заглушка в `HashSet` — обычно один и тот же статический объект `PRESENT`, который нужен просто как placeholder, потому что `Map` требует value
>

```java
set.add(x)  ->  map.put(x, PRESENT)
set.contains(x)  ->  map.containsKey(x)
```

### Methods Notes:

- *boolean* ~={green}add( ... )=~ - возвратит false, если элемент уже есть в Set ( и он не смог его добавить )


----
#### [[Set Interface Flashcards|Link to flashcards]]



---
### References:

- [[SortedSet Interface]]
