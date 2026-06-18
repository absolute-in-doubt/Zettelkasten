
25-05-2026 12:34

Status:

Tags: [[Testcontainers]] [[Testing]]

---
# configuration class - Testcontainers

```java
@TestConfiguration(proxyBeanMethods = false)  
@TestPropertySource(locations = "classpath:application-test.yaml")
@Testcontainers  
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


## Почему?

### 1. ~={purple}@Testcontainers=~ на классе

Аннотация `@Testcontainers` на классе `TestcontainersConfiguration` означает, что контейнеры будут запускаться **только для тестов, которые явно используют этот конфигурационный класс** (например, через `@Import` или `@ContextConfiguration`).

### 2. ~={purple}@Testcontainers=~ на тестовом классе

Если тестовый класс помечен `@Testcontainers`, но **не импортирует** `TestcontainersConfiguration`, то контейнеры из конфигурации **не запустятся**.

### 3. **Как сделать, чтобы контейнеры запускались для всех тестов?**

Нужно **явно подключить конфигурацию** к тестовому классу, например:

```java
@Testcontainers
@SpringBootTest
@Import(TestcontainersConfiguration.class)
public class MyIntegrationTest {
    // Теперь контейнеры из TestcontainersConfiguration будут запущены
}
```


----
#### [[configuration class - Testcontainers - Flashcards|Link to flashcards]]



---
### References:

