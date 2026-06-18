
17-05-2026 12:22

Status:

Tags: [[Caffeine + Spring Cache]] [[Spring Core]] [[Spring]]

---
# Настройка - Caffeine




Caffeine — high-performance in-memory cache для JVM.

Используется как:

- local cache
    
- Spring Cache provider
    
- replacement для Guava Cache



**Поддерживает**

- TTL expiration
    
- max size eviction
    
- LRU/LFU-like eviction
    
- async loading
    
- statistics
    
- refresh
    
- thread safety
    

---

### Зависимости

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>

<dependency>
    <groupId>com.github.ben-manes.caffeine</groupId>
    <artifactId>caffeine</artifactId>
</dependency>
```

---

### Главные сущности для работы с Caffeine

**Spring Cache**

```java
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.cache.CacheManager;
```

**Caffeine**

```java
import com.github.benmanes.caffeine.cache.Caffeine;
```

**Spring integration**

```java
import org.springframework.cache.caffeine.CaffeineCacheManager;
```

---

#### Включение cache (для чистого Spring)

```java
@EnableCaching
@Configuration
public class CacheConfig {
}
```


> [!note]
> Если используем обычный Spring Cache, то даже регистрировать `CaffeineCacheManager` вручную не обязательною Spring Cache найдет зависимость Caffeine и сам его создаст. Достаточно только включить Spring Cache через @EnableCaching.
>
> Ручное создание имеет смысл только для настройки кэша.

---

### Пример конфигурации

```java
@Bean
public CacheManager cacheManager() {

    CaffeineCacheManager manager =
            new CaffeineCacheManager();

    manager.setCaffeine(
            Caffeine.newBuilder()
                    .maximumSize(10_000)
                    .expireAfterWrite(Duration.ofMinutes(10))
                    .recordStats()
    );

    return manager;
}
```

---

# 8. Основные настройки Caffeine

---

## `maximumSize`

Максимальное количество элементов.

```java
.maximumSize(10_000)
```

При превышении:

```text
старые entries вытесняются
```

---

## `expireAfterWrite`

TTL после записи.

```java
.expireAfterWrite(Duration.ofMinutes(10))
```

---

## `expireAfterAccess`

TTL после последнего доступа.

```java
.expireAfterAccess(Duration.ofMinutes(30))
```

---

## `initialCapacity`

Начальный размер internal structures.

```java
.initialCapacity(100)
```

Полезно для уменьшения reallocations.

---

## `recordStats`

Включает cache statistics.

```java
.recordStats()
```

---

# 9. Разница expiration стратегий

|Strategy|Что делает|
|---|---|
|`expireAfterWrite`|удаляет через N времени после записи|
|`expireAfterAccess`|удаляет если долго не использовался|

---



# 12. Предсоздание cache

Можно явно указать caches:

```java
manager.setCacheNames(
        List.of("users", "products")
);
```

---

# Async cache

Caffeine поддерживает:

```java
AsyncLoadingCache
```

Но Spring Cache abstraction почти не использует async features.

---

# 17. Statistics

## Получение stats

```java
Cache cache = caffeineCache.getNativeCache();

cache.stats()
```

---

## Метрики

- hitRate
    
- missCount
    
- evictionCount
    
- averageLoadPenalty
    

---

# 18. Настройка через application.yml

## Spring Boot shortcut

```yaml
spring:
  cache:
    cache-names:
      - users
      - products

    caffeine:
      spec: maximumSize=10000,expireAfterWrite=10m
```

```yaml
spring:
  cache:
    type: caffeine
	
    cache-names:
      - users
      - products
      - orders
	
    caffeine:
      spec: >
        initialCapacity=100,
        maximumSize=10000,
        expireAfterAccess=30m,
        expireAfterWrite=1h,
        weakKeys,
        weakValues,
        recordStats
```


**~={red}?!=~** Здесь есть важное ограничение: через `application.yaml` Spring Boot умеет настраивать Caffeine **не всеми методами API `CaffeineBuilder`**, а только через строку спецификации (`CaffeineSpec`).

То есть:

```yaml
spring:
  cache:
    caffeine:
      spec: maximumSize=10000,expireAfterWrite=10m
```

Под капотом превращается в:

```java
Caffeine.from("maximumSize=10000,expireAfterWrite=10m")
```


---

# 19. Когда использовать Java config

Используй Java config если нужны:

- custom builder
    
- разные cache policies
    
- custom cache loader
    
- async cache
    
- custom expiry
    

---

# 20. Несколько cache с разными настройками

```java
@Bean
public CacheManager cacheManager() {

    SimpleCacheManager manager =
            new SimpleCacheManager();

    CaffeineCache users =
            new CaffeineCache(
                    "users",
                    Caffeine.newBuilder()
                            .expireAfterWrite(Duration.ofMinutes(5))
                            .build()
            );

    CaffeineCache products =
            new CaffeineCache(
                    "products",
                    Caffeine.newBuilder()
                            .maximumSize(1000)
                            .build()
            );

    manager.setCaches(List.of(users, products));

    return manager;
}
```

---


----
#### [[Настройка - Caffeine - Flashcards|Link to flashcards]]



---
### References:

