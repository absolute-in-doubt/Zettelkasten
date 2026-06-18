
19-03-2026 17:49

Status:

Tags: [[Redisson]] [[Spring Data JPA]] [[Hibernate & JPA]]

---
# Настройка Redisson в Spring Boot

Необходимые зависимости:

```xml
<!-- Source: https://mvnrepository.com/artifact/org.redisson/redisson-spring-boot-starter -->
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson-spring-boot-starter</artifactId>
    <version>4.3.0</version>
    <scope>compile</scope>
</dependency>
<!-- Source: https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>4.0.3</version>
    <scope>compile</scope>
</dependency>
```


### Пример конфигурации

```java
@Configuration
@EnableCaching
public class RedisCacheConfig {
    
    @Bean
    public RedissonClient redissonClient() {
        Config config = new Config();
        config.useSingleServer()
              .setAddress("redis://localhost:6379");
        return Redisson.create(config);
    }
    
    @Bean
    public CacheManager cacheManager(RedissonClient redissonClient) {
        Map<String, CacheConfig> config = new HashMap<>();
        config.put("users", CacheConfig.defaultCacheConfig()
                .entryTTL(Duration.ofMinutes(30)));
        config.put("categories", CacheConfig.defaultCacheConfig()
                .entryTTL(Duration.ofHours(2)));
        
        return new RedissonSpringCacheManager(redissonClient, config);
    }
}
```



----
#### [[Настройка Redisson в Spring Boot - Flashcards|Link to flashcards]]



---
### References:

