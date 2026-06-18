
12-06-2026 09:23

Status:

Tags: [[Caffeine + Spring Cache]] [[Spring Core]] [[Spring]]

---
# overview - Spring Cache


Spring Cache — это **абстракция кеширования**, входящая в Spring Framework. Она не является реализацией кеша сама по себе, а предоставляет единый API поверх Redis, Caffeine, Ehcache, Hazelcast и других провайдеров.

---

## Обязательные бины

#### Минимум нужен CacheManager

Spring Cache не сможет работать без реализации:

```java
org.springframework.cache.CacheManager
```

Это главный бин всей системы кеширования.

Примеры:

```java
RedisCacheManager
CaffeineCacheManager
JCacheCacheManager
ConcurrentMapCacheManager
```

Если используется Spring Boot, он обычно создается автоматически.

### [[Настройка - Caffeine|Настройка CaffeineCacheManager]]


---
## Главные интерфейсы Spring Cache


### `CacheManager`

Центральный бин.

```java
public interface CacheManager {
    Cache getCache(String name);
}
```

Задача:

```text
CacheManager
      ↓
получить нужный Cache
```

Пример:

```java
@Autowired
CacheManager cacheManager;
```


---
### `Cache`

Представляет конкретный кеш.

```java
Cache cache =
    cacheManager.getCache("users");
```

Операции:

```java
cache.get(key);

cache.put(key, value);

cache.evict(key);

cache.clear();
```


---
### `CacheResolver`

Определяет какой Cache использовать.

По умолчанию:

```text
CacheResolver
      ↓
CacheManager
      ↓
Cache
```

Можно реализовать самостоятельно. 


---
### `KeyGenerator`

Генерация ключей.

По умолчанию используется:

```java
SimpleKeyGenerator
```

Алгоритм:

```text
нет параметров
    ↓
SimpleKey.EMPTY

1 параметр
    ↓
сам объект

несколько параметров
    ↓
SimpleKey(...)
```

### [[Custom KeyGenerator - Spring Cache]]


---

### `CacheErrorHandler`

Обработка ошибок кеша.

По умолчанию:

```java
SimpleCacheErrorHandler
```

Если Redis упал:

```text
RedisConnectionException
        ↓
CacheErrorHandler
```

Можно логировать ошибки и не валить запрос.


---
## Включение кеширования

```java
@Configuration
@EnableCaching
public class CacheConfig {
}
```

Аннотация импортирует внутреннюю конфигурацию кеширования через:

```java
CachingConfigurationSelector
```

и регистрирует необходимые AOP-бины. 


---
### Почему нужен AOP

Spring Cache работает через прокси.

```text
Controller
      ↓
Cache Proxy
      ↓
Service
```

Поэтому есть ограничения:

### Работает

```java
service.getUser(1L);
```

### Не работает

```java
public User get() {
    return getUser(1L);
}
```

service calls it's own method -> Self-invocation.





---

# Работа с несколькими CacheManager

Допустим в контексте зарегистрированы:

```java
RedisCacheManager
CaffeineCacheManager
```

### Использование

```java
@Cacheable(
    cacheNames = "users",
    cacheManager = "redisCacheManager"
)
```

---

### `CacheResolver`: Когда выбор кеша зависит от аргументов.


```java
public class DynamicCacheResolver
       implements CacheResolver {
}
```

```java
@Cacheable(
    cacheResolver = "dynamicResolver"
)
```

`cacheManager` и `cacheResolver` взаимоисключающие настройки. ([Home](https://docs.spring.io/spring-framework/docs/4.3.14.RELEASE/spring-framework-reference/html/cache.html?utm_source=chatgpt.com "36. Cache Abstraction"))


---
## CachingConfigurer

Полная кастомизация инфраструктуры.

```java
@Configuration
@EnableCaching
public class CacheConfig
        implements CachingConfigurer {
}
```

Можно переопределить:

```java
CacheManager

CacheResolver

KeyGenerator

CacheErrorHandler
```

---

# 13. Spring Boot Autoconfiguration

Если есть:

```xml
<artifactId>
spring-boot-starter-cache
</artifactId>
```

Boot сам ищет провайдера кеша. 

Порядок поиска:

```text
Generic
JCache
Hazelcast
Infinispan
Couchbase
Redis
Caffeine
Cache2k
Simple
```


---

## Настройка имен кешей

```yaml
spring:
  cache:
    cache-names:
      - users
      - products
      - orders
```

Boot создаст их при старте. 

> [!warning]
> Spring может создавать кэши лениво:
>
>```
>@Cacheable("users")
>```
>
>Первый вызов:
>
>```
>getCache("users")
>```
>
>и кэш будет создан автоматически.
>
> **~={red}Указывать имена кэшей в `application.yaml` нужно только для fail-fast поведения.=~**



---

# 14. Simple Cache (по умолчанию)

Если ничего нет:

```java
ConcurrentMapCacheManager
```

Внутри:

```java
ConcurrentHashMap
```

```text
Application
   ↓
ConcurrentMapCacheManager
   ↓
ConcurrentHashMap
```

Только для разработки.

---

# 15. Caffeine

Самый популярный локальный кеш.

Зависимость:

```xml
spring-boot-starter-cache
caffeine
```

Boot создаст:

```java
CaffeineCacheManager
```

автоматически. ([Home](https://docs.spring.io/spring-boot/reference/io/caching.html?utm_source=chatgpt.com "Caching :: Spring Boot"))

---

# 16. Redis

Для распределенного кеша.

Boot создает:

```java
RedisCacheManager
```

автоматически при наличии Redis. ([Home](https://docs.spring.io/spring-boot/reference/io/caching.html?utm_source=chatgpt.com "Caching :: Spring Boot"))

Пример:

```yaml
spring:
  cache:
    type: redis
    redis:
      time-to-live: 10m
```

TTL:

```text
10 минут
```

для всех кешей. ([Home](https://docs.spring.io/spring-boot/reference/io/caching.html?utm_source=chatgpt.com "Caching :: Spring Boot"))

---

---

# 18. CacheManagerCustomizer

Позволяет донастроить автоматически созданный CacheManager.

```java
@Bean
CacheManagerCustomizer<
        ConcurrentMapCacheManager> customizer() {

    return manager ->
        manager.setAllowNullValues(false);
}
```


---



### Какие обязательные бины нужны?

Минимально:

```java
CacheManager
```

Остальное Spring создаст сам.



----
#### [[overview - Spring Cache - Flashcards|Link to flashcards]]



---
### References:

