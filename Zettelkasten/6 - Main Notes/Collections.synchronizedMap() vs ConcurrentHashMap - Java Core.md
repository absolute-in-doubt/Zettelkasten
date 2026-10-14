
19-09-2026 10:24

Status:

Tags: [[Java Core]]

---
# Collections.synchronizedMap() vs ConcurrentHashMap - Java Core


`Collections.synchronizedMap()` — это обычная `Map`, обёрнутая общим монитором. `ConcurrentHashMap` изначально спроектирован для конкурентного доступа и обычно обеспечивает значительно лучшую масштабируемость при работе нескольких потоков.
	
Методы `Collections.synchronizedMap()` синхронизируются на одном общем объекте. Поэтому одновременно с map фактически работает только один поток.
	
**Итерация**
	
Для `synchronizedMap` синхронизации отдельных методов недостаточно. Итерация состоит из нескольких шагов, поэтому её нужно защищать вручную:
	
```java
Map<String, Integer> map =
        Collections.synchronizedMap(new HashMap<>());
	
synchronized (map) {
    for (Map.Entry<String, Integer> entry : map.entrySet()) {
        System.out.println(entry);
    }
}
```
	
Без `synchronized (map)` другой поток может изменить коллекцию во время обхода, и итератор может выбросить `ConcurrentModificationException`. Документация Java прямо требует синхронизировать доступ к collection views при итерации.
	
В `ConcurrentHashMap` итераторы являются weakly consistent: они не создают полноценный snapshot, могут отражать часть изменений, произошедших во время обхода, но не выбрасывают `ConcurrentModificationException` из-за таких изменений:
	
```java
ConcurrentHashMap<String, Integer> map =
        new ConcurrentHashMap<>();
	
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry);
}
```
	
---
**Compound-операции**
	
Проблема есть не только в отдельных `get()` и `put()`, а в последовательностях операций.
	
Потенциально небезопасный вариант:

```java
if (!map.containsKey(key)) {
    map.put(key, value);
}
```
	
Даже если отдельные вызовы потокобезопасны, последовательность «проверить и добавить» не является атомарной: два потока могут одновременно не найти ключ и оба вызвать `put`.
	
С `ConcurrentHashMap` следует использовать специальные методы:
	
```java
ConcurrentHashMap<String, Integer> map =
        new ConcurrentHashMap<>();
	
map.putIfAbsent("counter", 0);
	
map.compute("counter", (key, oldValue) ->
        oldValue == null ? 1 : oldValue + 1);
        
ConcurrentHashMap<String, User> cache =
        new ConcurrentHashMap<>();
	
User user = cache.computeIfAbsent(userId, id -> loadUser(id));
```


## Что выбрать

Используйте `ConcurrentHashMap`, если:

- map одновременно читают и изменяют несколько потоков;
    
- важна производительность при конкуренции;
    
- нужны `computeIfAbsent`, `merge`, `putIfAbsent`;
    
- не нужны `null`-ключи и `null`-значения;
    
- нужна безопасная итерация без обязательной блокировки всей map.
    

Используйте `Collections.synchronizedMap()`, если:

- уже есть конкретная реализация `Map`, которую нужно обернуть;
    
- конкуренция небольшая;
    
- нужна простая синхронизация всей коллекции;
    
- важно сохранить поведение или порядок исходной map;
    
- требуется поддержка `null`, которую предоставляет backing map.



----
#### [[Collections.synchronizedMap() vs ConcurrentHashMap - Java Core - Flashcards|Link to flashcards]]



---
### References:

