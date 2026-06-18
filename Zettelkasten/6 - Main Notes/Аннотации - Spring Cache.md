
12-06-2026 09:25

Status:

Tags: [[Caffeine + Spring Cache]] [[Spring Core]] [[Spring]]

---
# Аннотации - Spring Cache



## ~={purple}@Cacheable=~

Чтение из кеша.

```java
@Cacheable("users")
public User getUser(Long id)
```

Алгоритм:

```text
Есть значение?
    ↓
Да → вернуть
Нет → выполнить метод
          ↓
      сохранить в кеш
```

---

## ~={purple}@CachePut=~

Всегда выполняет метод и обновляет кеш.

```java
@CachePut(value = "users", key = "#user.id")
public User update(User user)
```

```text
Выполнить метод
      ↓
Обновить кеш
```

---

## ~={purple}@CacheEvict=~

Удаление из кеша.

```java
@CacheEvict(value = "users", key = "#id")
public void delete(Long id)
```

или

```java
@CacheEvict(
    value = "users",
    allEntries = true
)
```

Очистка всего региона кеша.


---
## ~={purple}@Caching=~

Комбинирование операций.

```java
@Caching(
    evict = {
        @CacheEvict("users"),
        @CacheEvict("profiles")
    }
)
```


---
## ~={purple}@CacheConfig=~

Общие настройки для класса.

```java
@Service
@CacheConfig(cacheNames = "users")
public class UserService {
}
```

Позволяет задавать:

- cacheNames
    
- keyGenerator
    
- cacheManager
    
- cacheResolver
    

на уровне класса.


----
#### [[Аннотации - Spring Cache - Flashcards|Link to flashcards]]



---
### References:

