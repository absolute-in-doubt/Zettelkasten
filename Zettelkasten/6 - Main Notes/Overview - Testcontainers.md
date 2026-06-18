
19-03-2026 18:31

Status:

Tags: [[Testcontainers]] [[Testing]]

---
# Overview - Testcontainers


Testcontainers — библиотека для **интеграционного тестирования** с реальными сервисами (БД, Redis, Kafka) в Docker контейнерах. Автоматически поднимает/останавливает сервисы на время тестов.

## Главные абстракции JUnit Jupiter

#### **~={purple}@Testcontainers=~** (class level) 

**Включает Testcontainers extension** для JUnit 5. Обязательна для всех тестовых классов.

```java
@SpringBootTest
@Testcontainers  // ← включает TC extension
class UserRepositoryTest { ... }
```

---
### **~={purple}@Container=~** (поля) 

**Объявляет Docker контейнер**. JUnit автоматически управляет его жизненным циклом:

> [!tip] Note
> `@Testcontainers` + `@Container` - управляют исключительно жизненным циклом. 
>
> То есть можно и без них - вручную.


- `start()` перед тестами
- `stop()` после тестов
- **Shared между методами класса** (экономия времени)

```java
@Testcontainers
class DatabaseTest {
    
    @Container           // ← контейнер PostgreSQL
    static PostgreSQLContainer<?> postgres = 
        new PostgreSQLContainer<>("postgres:16")
            .withDatabaseName("testdb")
            .withUsername("user")
            .withPassword("pass");
    
    @Container           // ← контейнер Redis  
    static RedisContainer redis = new RedisContainer("redis:7");
}
```

#### Режимы работы контейнеров

| Режим          | Аннотация           | Поведение                               |
| -------------- | ------------------- | --------------------------------------- |
| **Reuse**      | `static final` поле | Один контейнер на **ВСЕ** тесты проекта |
| **Shared**     | `static` поле       | Один на **класс** тестов                |
| **Per-method** | обычное поле        | Новый контейнер для **каждого** `@Test` |

```java
// Reuse (быстрее всего)
@Container
static final PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16");

// Shared в классе  
@Container
static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16");

// Новый для каждого теста (медленно)
@Container
PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16");
```


---
### **~={purple}@DynamicPropertySource=~**

**Подставляет порты/URL** контейнеров в `application.properties` на лету.

```java
@DynamicPropertySource
static void configure(DynamicPropertyRegistry registry) {
    registry.add("spring.datasource.url", postgres::getJdbcUrl);
    registry.add("spring.datasource.username", postgres::getUsername);
    registry.add("spring.datasource.password", postgres::getPassword);
    
    registry.add("spring.data.redis.host", redis::getHost);
    registry.add("spring.data.redis.port", redis::getFirstMappedPort);
}
```

## Полный пример


```java
@SpringBootTest
@Testcontainers
class FullStackTest {
    
    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16")
            .withDatabaseName("test");
    
    @Container  
    static RedisContainer redis = new RedisContainer("redis:7");
    
    @DynamicPropertySource
    static void props(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.data.redis.host", redis::getHost);
        registry.add("spring.data.redis.port", redis::getFirstMappedPort);
    }
    
    @Autowired UserRepository repo;
    
    @Test
    void testCacheWorks() {
        // реальный Postgres + реальный Redis!
        User user = repo.save(new User(1L, "Test"));
        User cached = repo.findById(1L).orElse(null); // из Redis
    }
}
```

## Жизненный цикл

```
1. @BeforeAll → postgres.start(), redis.start()
2. Каждый @Test → использует запущенные контейнеры
3. @AfterAll → postgres.stop(), redis.stop() + очистка
```

**Преимущества**: реальные сервисы, изоляция тестов, никаких моков!


----
#### [[overview - Testcontainers - Flashcards|Link to flashcards]]



---
### References:

