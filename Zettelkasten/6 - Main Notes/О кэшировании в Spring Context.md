
16-05-2026 11:06

Status:

Tags: [[Кэширование]] [[Spring]]

---
# О кэшировании в Spring Context




### Какие зависимости нужны


```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

Starter подключает:

- spring-context
    
- spring-aop
    
- ~={orange}cache abstraction=~


---

### Дополнительный cache provider

Spring сам НЕ хранит кэш.  
Он только предоставляет abstraction layer.

Нужен provider:

**Популярные реализации**

|Provider|Зависимость|
|---|---|
|ConcurrentHashMap|встроен|
|Caffeine|`caffeine`|
|Ehcache|`ehcache`|
|Redis|`spring-boot-starter-data-redis`|
|Hazelcast|`hazelcast`|

---

## Пример Caffeine

```xml
<dependency>
    <groupId>com.github.ben-manes.caffeine</groupId>
    <artifactId>caffeine</artifactId>
</dependency>
```

---

### Включение кэширования

```java
@EnableCaching
@Configuration
public class CacheConfig {
}
```

`@EnableCaching` запускает инфраструктуру Spring Cache.

> [!note]
> Нужно только для чистого Spring

---

### Что происходит под капотом

## `@EnableCaching` импортирует конфигурацию

Подключается:

```text
CachingConfigurationSelector
```

Он регистрирует бины инфраструктуры.

---

### Какие бины создаются

Spring создает:

|Бин|Назначение|
|---|---|
|`CacheInterceptor`|перехватывает вызовы|
|`CacheOperationSource`|хранит metadata аннотаций|
|`BeanFactoryCacheOperationSourceAdvisor`|связывает AOP и cache|
|`CacheManager`|управляет кэшами|
|Proxy bean|прокси над вашим сервисом|

---

### Как работает `@Cacheable`

## Пример

```java
@Cacheable("users")
public User findById(Long id) {
    return repository.find(id);
}
```

---

## Последовательность вызова

```text
Client
  ↓
Proxy
  ↓
CacheInterceptor
  ↓
CacheManager
  ↓
Cache lookup
```

---

### `CacheManager`

Главный abstraction bean.

```java
public interface CacheManager {
    Cache getCache(String name);
}
```

Он:

- создает cache
    
- хранит cache instances
    
- управляет provider’ом


---

### Пример с ConcurrentMapCacheManager

Если provider не указан:

```java
ConcurrentMapCacheManager
```

Внутри:

```java
ConcurrentHashMap
```

Это простой in-memory cache.

---

### Как Spring выбирает CacheManager

Spring ищет bean:

```java
CacheManager
```

Например:

```java
@Bean
public CacheManager cacheManager() {
    return new CaffeineCacheManager();
}
```


---

### Минимальный пример

```java
@EnableCaching
@SpringBootApplication
public class App {
}
```

```java
@Service
public class UserService {

    @Cacheable("users")
    public User getUser(Long id) {
        System.out.println("DB call");
        return repository.find(id);
    }
}
```

Первый вызов:

```text
DB call
```

Второй:

```text
из cache
```


----
#### [[О кэшировании в Spring Context - Flashcards|Link to flashcards]]



---
### References:

