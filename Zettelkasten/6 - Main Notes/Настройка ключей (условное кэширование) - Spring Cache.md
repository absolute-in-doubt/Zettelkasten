
12-06-2026 09:35

Status:

Tags: [[Caffeine + Spring Cache]] [[Spring Core]] [[Spring]]

---
# Настройка ключей (условное кэширование) - Spring Cache


Использует [[SpEL - Spring expressions|SpEL]].

## Через key

```java
@Cacheable(
    value = "users",
    key = "#id"
)
```

---

### Несколько параметров

```java
@Cacheable(
    value = "users",
    key = "#id + ':' + #country"
)
```

---

### Использование результата

```java
@CachePut(
    value = "users",
    key = "#result.id"
)
```

---

# Условное кеширование

## condition

До выполнения метода.

```java
@Cacheable(
    value = "users",
    condition = "#id > 0"
)
```

---

## unless

После выполнения метода.

```java
@Cacheable(
    value = "users",
    unless = "#result == null"
)
```

Например не кешировать null.  (так делать не надо -> [[Caching pitfalls - System Design#Cache penetration|cache penetration]])


----
#### [[Настройка ключей (условное кэширование) - Spring Cache - Flashcards|Link to flashcards]]



---
### References:

- [[Custom KeyGenerator - Spring Cache]]