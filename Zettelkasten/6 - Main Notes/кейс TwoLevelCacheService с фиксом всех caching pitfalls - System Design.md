
19-04-2026 12:12

Status:

Tags: [[Кэширование]]

---
# кейс TwoLevelCacheService с фиксом всех caching pitfalls - System Design

Для избежания [[Caching pitfalls - System Design#Thundering Herd - одновременное обновление кучей потоков|Thundering Herd - одновременного обновления значения кучей потоков]] используем возврат stale value и асинхронный пересчёт нового при новом запросе этого значения (пока stale ещё хранится). 

Вот структуры хранимого в Redis объекта:

```json
{
  "data": { ... },
  "freshUntil": 1713528000000,
  "staleUntil": 1713528300000
}
```

Вот "DTO", которое будет давать нужную структуру сообщения:

```java
public record CacheEnvelope<T>(
        T data,
        long freshUntil,
        long staleUntil
) {
    boolean isFresh(long now) {
        return now < freshUntil;
    }

    boolean isStaleButUsable(long now) {
        return now >= freshUntil && now < staleUntil;
    }

    boolean isExpired(long now) {
        return now >= staleUntil;
    }
}
```


Сам сервис:

```java
@Service
public class TwoLevelCacheService {

    private final Cache<String, Object> caffeine;
    private final StringRedisTemplate redis;
    private final ObjectMapper objectMapper;
    private final Executor refreshExecutor;

    public TwoLevelCacheService(CaffeineCacheManager caffeineCacheManager,
                                StringRedisTemplate redis,
                                ObjectMapper objectMapper,
                                @Qualifier("cacheRefreshExecutor") Executor refreshExecutor) {
        this.caffeine = caffeineCacheManager.getCache("l1").getNativeCache();
        this.redis = redis;
        this.objectMapper = objectMapper;
        this.refreshExecutor = refreshExecutor;
    }

    public <T> T get(String key, Class<T> type, Supplier<T> dbLoader,
                     Duration freshTtl, Duration staleTtl) {
        long now = System.currentTimeMillis();

        T l1 = getL1(key, type);
        if (l1 != null) return l1;

        CacheEnvelope<T> env = getL2(key, type);
        if (env != null) {
            if (env.isFresh(now)) {
                putL1(key, env.data());
                return env.data();
            }
            if (env.isStaleButUsable(now)) {
                putL1(key, env.data());
                refreshAsync(key, type, dbLoader, freshTtl, staleTtl);
                return env.data();
            }
        }

        return loadAndStoreWithLockAndDoubleCheck(key, type, dbLoader, freshTtl, staleTtl);
    }

    private <T> T loadAndStoreWithLockAndDoubleCheck(String key, Class<T> type,
                                             Supplier<T> dbLoader,
                                             Duration freshTtl, Duration staleTtl) {
        String lockKey = "lock:" + key;
        boolean acquired = Boolean.TRUE.equals(
                redis.opsForValue().setIfAbsent(lockKey, "1", Duration.ofSeconds(10))
        );

        if (!acquired) {
            return waitForCachedValue(key, type, dbLoader, freshTtl, staleTtl);
        }

        try {
            T l1 = getL1(key, type);
            if (l1 != null) return l1;

            CacheEnvelope<T> env = getL2(key, type);
            if (env != null && !env.isExpired(System.currentTimeMillis())) {
                putL1(key, env.data());
                return env.data();
            }

            T value = dbLoader.get();
            if (value != null) {
                store(key, value, freshTtl, staleTtl);
                putL1(key, value);
            }
            return value;
        } finally {
            redis.delete(lockKey);
        }
    }

    private <T> T waitForCachedValue(String key, Class<T> type,
                                     Supplier<T> dbLoader,
                                     Duration freshTtl, Duration staleTtl) {
        for (int i = 0; i < 20; i++) {
            T l1 = getL1(key, type);
            if (l1 != null) return l1;

            CacheEnvelope<T> env = getL2(key, type);
            if (env != null && !env.isExpired(System.currentTimeMillis())) {
                putL1(key, env.data());
                return env.data();
            }

            try {
                Thread.sleep(50L);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                break;
            }
        }
        return loadWithLockAndDoubleCheck(key, type, dbLoader, freshTtl, staleTtl);
    }

    private <T> void refreshAsync(String key, Class<T> type, Supplier<T> dbLoader,
                                  Duration freshTtl, Duration staleTtl) {
        CompletableFuture.runAsync(() -> {
            loadWithLockAndDoubleCheck(key, type, dbLoader, freshTtl, staleTtl);
        }, refreshExecutor);
    }

    private <T> T getL1(String key, Class<T> type) {
        ValueWrapper wrapper = caffeine.get(key);
        return wrapper == null ? null : type.cast(wrapper.get());
    }

    private <T> CacheEnvelope<T> getL2(String key, Class<T> type) {
        try {
            String json = redis.opsForValue().get(key);
            if (json == null) return null;

            JavaType javaType = objectMapper.getTypeFactory()
                    .constructParametricType(CacheEnvelope.class, type);

            return objectMapper.readValue(json, javaType);
        } catch (Exception e) {
            return null;
        }
    }

    private void putL1(String key, Object value) {
        caffeine.put(key, value);
    }

    private <T> void store(String key, T value, Duration freshTtl, Duration staleTtl) {
        long now = System.currentTimeMillis();
        CacheEnvelope<T> env = new CacheEnvelope<>(
                value,
                now + freshTtl.toMillis(),
                now + staleTtl.toMillis()
        );
        try {
            redis.opsForValue().set(key, objectMapper.writeValueAsString(env), staleTtl);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}

@Service
public class TwoLevelCacheService {

    private final Cache<String, Object> caffeine;
    private final StringRedisTemplate redis;
    private final ObjectMapper objectMapper;
    private final Executor refreshExecutor;

    public TwoLevelCacheService(CaffeineCacheManager caffeineCacheManager,
                                StringRedisTemplate redis,
                                ObjectMapper objectMapper,
                                @Qualifier("cacheRefreshExecutor") Executor refreshExecutor) {
        this.caffeine = caffeineCacheManager.getCache("l1").getNativeCache();
        this.redis = redis;
        this.objectMapper = objectMapper;
        this.refreshExecutor = refreshExecutor;
    }

    public <T> T get(String key, Class<T> type, Supplier<T> dbLoader,
                     Duration freshTtl, Duration staleTtl) {
        long now = System.currentTimeMillis();

        T l1 = getL1(key, type);
        if (l1 != null) return l1;

        CacheEnvelope<T> env = getL2(key, type);
        if (env != null) {
            if (env.isFresh(now)) {
                putL1(key, env.data());
                return env.data();
            }
            if (env.isStaleButUsable(now)) {
                putL1(key, env.data());
                refreshAsync(key, type, dbLoader, freshTtl, staleTtl);
                return env.data();
            }
        }

        return loadWithLockAndDoubleCheck(key, type, dbLoader, freshTtl, staleTtl);
    }

    private <T> T loadWithLockAndDoubleCheck(String key, Class<T> type,
                                             Supplier<T> dbLoader,
                                             Duration freshTtl, Duration staleTtl) {
        String lockKey = "lock:" + key;
        boolean acquired = Boolean.TRUE.equals(
                redis.opsForValue().setIfAbsent(lockKey, "1", Duration.ofSeconds(10))
        );

        if (!acquired) {
            return waitForCachedValue(key, type, dbLoader, freshTtl, staleTtl);
        }

        try {
            T l1 = getL1(key, type);
            if (l1 != null) return l1;

            CacheEnvelope<T> env = getL2(key, type);
            if (env != null && !env.isExpired(System.currentTimeMillis())) {
                putL1(key, env.data());
                return env.data();
            }

            T value = dbLoader.get();
            if (value != null) {
                store(key, value, freshTtl, staleTtl);
                putL1(key, value);
            }
            return value;
        } finally {
            redis.delete(lockKey);
        }
    }

    private <T> T waitForCachedValue(String key, Class<T> type,
                                     Supplier<T> dbLoader,
                                     Duration freshTtl, Duration staleTtl) {
        for (int i = 0; i < 20; i++) {
            T l1 = getL1(key, type);
            if (l1 != null) return l1;

            CacheEnvelope<T> env = getL2(key, type);
            if (env != null && !env.isExpired(System.currentTimeMillis())) {
                putL1(key, env.data());
                return env.data();
            }

            try {
                Thread.sleep(50L);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                break;
            }
        }
        return loadWithLockAndDoubleCheck(key, type, dbLoader, freshTtl, staleTtl);
    }

    private <T> void refreshAsync(String key, Class<T> type, Supplier<T> dbLoader,
                                  Duration freshTtl, Duration staleTtl) {
        CompletableFuture.runAsync(() -> {
            loadWithLockAndDoubleCheck(key, type, dbLoader, freshTtl, staleTtl);
        }, refreshExecutor);
    }

    private <T> T getL1(String key, Class<T> type) {
        ValueWrapper wrapper = caffeine.get(key);
        return wrapper == null ? null : type.cast(wrapper.get());
    }

    private <T> CacheEnvelope<T> getL2(String key, Class<T> type) {
        try {
            String json = redis.opsForValue().get(key);
            if (json == null) return null;

            JavaType javaType = objectMapper.getTypeFactory()
                    .constructParametricType(CacheEnvelope.class, type);

            return objectMapper.readValue(json, javaType);
        } catch (Exception e) {
            return null;
        }
    }

    private void putL1(String key, Object value) {
        caffeine.put(key, value);
    }

    private <T> void store(String key, T value, Duration freshTtl, Duration staleTtl) {
        long now = System.currentTimeMillis();
        CacheEnvelope<T> env = new CacheEnvelope<>(
                value,
                now + freshTtl.toMillis(),
                now + staleTtl.toMillis()
        );
        try {
            redis.opsForValue().set(key, objectMapper.writeValueAsString(env), staleTtl);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```


Конфиг Executor (для асинхронного обновления):

```java
@Configuration
public class CacheExecutorConfig {

    @Bean(name = "cacheRefreshExecutor")
    public Executor cacheRefreshExecutor() {
        return Executors.newFixedThreadPool(4);
    }
}
```

---
### Пример использования

```java
@Service
public class UserService {

    private final TwoLevelCacheService cache;
    private final UserRepository userRepository;

    public UserService(TwoLevelCacheService cache, UserRepository userRepository) {
        this.cache = cache;
        this.userRepository = userRepository;
    }

    public UserDto getUser(Long id) {
        return cache.get(
                "user:" + id,
                UserDto.class,
                () -> userRepository.findDtoById(id).orElse(null),
                Duration.ofMinutes(5),
                Duration.ofMinutes(10)
        );
    }
}
```

----
#### [[кейс TwoLevelCacheService с фиксом всех caching pitfalls - System Design - Flashcards|Link to flashcards]]



---
### References:

