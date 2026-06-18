
12-06-2026 09:38

Status:

Tags: [[Caffeine + Spring Cache]] [[Spring Core]] [[Spring]]

---
# Custom KeyGenerator - Spring Cache



```java
@Bean
public KeyGenerator customKeyGenerator() {
    return (target, method, params) ->
        method.getName()
            + Arrays.toString(params);
}
```

Использование:

```java
@Cacheable(
    cacheNames = "users",
    keyGenerator = "customKeyGenerator"
)
```

**~={red}?!=~** `key` и `keyGenerator` одновременно использовать нельзя. 


----
#### [[Custom KeyGenerator - Spring Cache - Flashcards|Link to flashcards]]



---
### References:

- [[Настройка ключей (условное кэширование) - Spring Cache]]