
25-05-2026 12:34

Status:

Tags: [[Testcontainers]] [[Testing]]

---
# configuration class - Testcontainers

```java
@TestConfiguration(proxyBeanMethods = false)  
@TestPropertySource(locations = "classpath:application-test.yaml")
public class TestcontainersConfiguration {  
      
  
    @Bean  
    @ServiceConnection    
    PostgreSQLContainer<?> postgresContainer() {  
       return new PostgreSQLContainer<>(DockerImageName.parse("postgres:15"));  
    }  
  
    @Bean  
    @ServiceConnection    
    RedisContainer redisContainer() {  
       RedisContainer container = new RedisContainer(DockerImageName.parse("redis:latest"))  
             .withExposedPorts(6379);  
       container.start();  
       return container;  
    }  
}
```


> [!warning]
> Контейнеры, сконфигурированные в отдельном `@TestConfiguration` классе, **не будут автоматически запускаться** для всех тестов, помеченных `@Testcontainers`.

~={pink}Надо ли аннотировать @Testcontainers и класс конфигурации и класс теста, куда он импортируется через @Import?=~
	_Нет, **`@Testcontainers` нужно ставить только на тестовый класс**, а не на класс конфигурации с контейнерами._

```java
@TestConfiguration(proxyBeanMethods = false)
class TestcontainersConfiguration {

    @Bean
    @ServiceConnection
    PostgreSQLContainer<?> postgresContainer() {
        return new PostgreSQLContainer<>("postgres:16-alpine");
    }
}
```

```java
@SpringBootTest
@Testcontainers
@Import(TestcontainersConfiguration.class)
class MyIntegrationTest {

    // тесты...
}
```


----
#### [[configuration class - Testcontainers - Flashcards|Link to flashcards]]



---
### References:

