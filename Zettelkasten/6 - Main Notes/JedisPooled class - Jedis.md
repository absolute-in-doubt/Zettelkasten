
10-10-2025 12:54

Status: #not_even_born 

Tags: [[Jedis]] [[Java+]]

---
# JedisPooled class - Jedis


## Что такое `JedisPooled`

`JedisPooled` — это удобный обёрточный класс, добавленный начиная с **Jedis 4.x**,  
который:

- внутри содержит **`JedisPool`** (пул соединений),
    
- реализует интерфейс `UnifiedJedis` (для унифицированного API),
    
- **сам управляет получением и возвратом соединений в пул** при каждой операции.
    

👉 То есть вы не работаете напрямую с `JedisPool` и `Jedis` ресурсами.  
Вместо этого вы просто пишете:

```java
JedisPooled jedis = new JedisPooled("localhost", 6379);
jedis.set("key", "value");
String value = jedis.get("key");
```

а он уже **внутри**:

- берёт соединение из пула,
- выполняет команду,    
- возвращает соединение обратно в пул.

----
#### [[JedisPooled class - Jedis - Flashcards|Link to flashcards]]



---
### References:

- [[Jedis class - Jedis]]