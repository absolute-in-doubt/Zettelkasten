
18-05-2026 09:25

Status:

Tags: [[Spring Data Redis]] [[Spring]]

---
# RedisTemplate methods - Spring Data Redis

~={red}**RedisTemplate - thread safe**=~


##### Основные группы методов

| Метод           | Redis structure |
| --------------- | --------------- |
| `opsForValue()` | String          |
| `opsForHash()`  | Hash            |
| `opsForList()`  | List            |
| `opsForSet()`   | Set             |
| `opsForZSet()`  | Sorted Set      |


---
### opsForValue()

Работа со string values.


```java
//SET
opsForValue().set("key", "value");

//SET + TTL
opsForValue().set(
    "key",
    "value",
    Duration.ofMinutes(5)
);

//GET
opsForValue().get("key");

//INCREMENT
opsForValue().increment("counter");

//DECREMENT
opsForValue().decrement("counter");
```

> [!note]
> В Redis все значения хранятся в виде String, но технически, если значение чистое число, то в памяти оно хранится как число (так место экономится) -> можно инкрементить/декрементить такие численные значения

#### `setIfAbsent`

Аналог:

```text
SETNX
```

```java
opsForValue().setIfAbsent("key", "value");
```

|Результат|Значение|
|---|---|
|`true`|ключа не было, значение установлено|
|`false`|ключ уже существовал|
|`null`|ошибка / pipeline / transaction context|


---

### opsForHash()

Работа с Redis Hash.


```java
//PUT
opsForHash().put(
    "users",
    "1",
    user
);

//GET
opsForHash().get("users", "1");

//ENTRIES
opsForHash().entries("users");

//DELETE FIELD
opsForHash().delete("users", "1");
```

---

# 4. opsForList()

Redis List.

---

## leftPush

```java
opsForList().leftPush("queue", "task");
```

---

## rightPush

```java
opsForList().rightPush("queue", "task");
```

---

## pop

```java
opsForList().leftPop("queue");
```

---

## range

```java
opsForList().range("queue", 0, 10);
```

---

# 5. opsForSet()

Redis Set.

---

## add

```java
opsForSet().add("tags", "java");
```

---

## members

```java
opsForSet().members("tags");
```

---

## remove

```java
opsForSet().remove("tags", "java");
```

---

## isMember

```java
opsForSet().isMember("tags", "java");
```

---

# 6. opsForZSet()

Sorted Set.

---

## add with score

```java
opsForZSet().add(
    "rating",
    "john",
    100
);
```

---

## range

```java
opsForZSet().range("rating", 0, 10);
```

---

## reverseRange

```java
opsForZSet().reverseRange("rating", 0, 10);
```

---

## score

```java
opsForZSet().score("rating", "john");
```

---

# 7. Общие методы RedisTemplate

## delete

```java
redisTemplate.delete("key");
```

---

## hasKey

```java
redisTemplate.hasKey("key");
```

---

## expire

```java
redisTemplate.expire(
    "key",
    Duration.ofMinutes(5)
);
```

---

## getExpire

```java
redisTemplate.getExpire("key");
```

---

## rename

```java
redisTemplate.rename("old", "new");
```

---

# 8. Transactions

---

## multi

```java
redisTemplate.multi();
```

---

## exec

```java
redisTemplate.exec();
```

---

# 9. Pipeline

Batch execution.

---

## executePipelined

```java
redisTemplate.executePipelined(...)
```

---

# 10. Lua scripts

---

## execute

```java
redisTemplate.execute(script, keys, args);
```

---

# 11. Bound operations

Привязка к конкретному key.

---

## Пример

```java
BoundValueOperations<String, String> ops =
    redisTemplate.boundValueOps("counter");
```

---

# После этого

```java
ops.increment();
ops.get();
```

---

# 12. execute()

Низкоуровневый API.

---

## Пример

```java
redisTemplate.execute(connection -> {
    ...
});
```

---

# 13. Serializer methods

---

## setKeySerializer

```java
template.setKeySerializer(...)
```

---

## setValueSerializer

```java
template.setValueSerializer(...)
```

---

# 14. Часто используемые методы

|Метод|Использование|
|---|---|
|`opsForValue().get/set`|cache|
|`increment`|counters|
|`setIfAbsent`|distributed lock|
|`expire`|TTL|
|`delete`|invalidation|
|`opsForHash()`|object storage|

---

# 15. Важная особенность

`RedisTemplate`:

```text
thread-safe
```

и обычно используется как singleton bean.



----
#### [[RedisTemplate methods - Spring Data Redis - Flashcards|Link to flashcards]]



---
### References:

