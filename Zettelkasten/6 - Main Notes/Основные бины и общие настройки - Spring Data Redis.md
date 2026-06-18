
18-05-2026 09:15

Status:

Tags: [[Spring Data Redis]] [[Spring]]

---
# Основные бины и общие настройки 


#### Что подключает starter

- Spring Data Redis
- RedisTemplate
- RedisConnectionFactory
- Lettuce client (default)

#### Основные бины

| Бин                        | Назначение                 |
| -------------------------- | -------------------------- |
| `RedisConnectionFactory`   | создание Redis connections |
| `RedisTemplate<K,V>`       | основной API для Redis     |
| `StringRedisTemplate`      | RedisTemplate для строк    |
| `LettuceConnectionFactory` | default implementation     |
| `RedisCacheManager`        | Spring Cache               |


> [!note] 
> `StringRedisTemplate` - то же самое, что и `RedisTemplate<String, String>`:
> Всегда использует `StringRedisSerializer` - **~={orange}просто заранее настроенные serializers=~**




# Основные serializer'ы

| Serializer                           | Назначение         |
| ------------------------------------ | ------------------ |
| `StringRedisSerializer`              | строки             |
| `GenericJackson2JsonRedisSerializer` | JSON               |
| `JdkSerializationRedisSerializer`    | Java serialization |


#### Настройка кастомной Json сериализации

```java
@Bean
RedisTemplate<String, Object> redisTemplate(
        RedisConnectionFactory factory
) {

    RedisTemplate<String, Object> template =
            new RedisTemplate<>();

    template.setConnectionFactory(factory);

    template.setValueSerializer(
            new GenericJackson2JsonRedisSerializer()
    );

    return template;
}
```


---
### Настройки подключения


##### Через `application.yaml`

```yaml
spring:
  data:
    redis:
      host: localhost
      port: 6379
      password: secret  --- не обязательно ---
      database: 1
      timeout: 2s
      ssl:  
		enabled: true
```


##### Java config


**RedisStandaloneConfig:**
```java
@Bean
public RedisConnectionFactory redisConnectionFactory() {

    RedisStandaloneConfiguration config =
            new RedisStandaloneConfiguration();

    config.setHostName("localhost");
    config.setPort(6379);
    config.setPassword("secret");

    return new LettuceConnectionFactory(config);
}
```


**LettuceClientConfiguration:**
```java
@Bean
public RedisConnectionFactory redisConnectionFactory() {

    RedisStandaloneConfiguration redisConfig =
            new RedisStandaloneConfiguration(
                    "localhost",
                    6379
            );

    LettuceClientConfiguration clientConfig =
            LettuceClientConfiguration.builder()
                    .commandTimeout(Duration.ofSeconds(2))
                    .shutdownTimeout(Duration.ZERO)
                    .build();

    return new LettuceConnectionFactory(
            redisConfig,
            clientConfig
    );
}
```

----
#### [[Основные бины и общие настройки - Spring Data Redis - Flashcards|Link to flashcards]]



---
### References:

