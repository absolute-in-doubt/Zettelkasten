
22-05-2026 22:22

Status:

Tags: [[Testcontainers]] [[Testing]]

---
# Overview - Postgres Testcontainer


```xml
<dependencies>
    <!-- Spring Test -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- Testcontainers Core -->
    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>testcontainers</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- PostgreSQL module -->
    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>postgresql</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- JUnit integration -->
    <dependency>
        <groupId>org.testcontainers</groupId>
        <artifactId>junit-jupiter</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```


---
### Базовый принцип работы

Testcontainers:

1. запускает Docker-контейнер
    
2. ждёт readiness
    
3. отдаёт:
    
    - JDBC URL
        
    - username
        
    - password
        
4. Spring подключается к этой БД


---

### Самый распространённый способ — `PostgreSQLContainer`

Основной класс:

```java
PostgreSQLContainer<?> postgres =
        new PostgreSQLContainer<>("postgres:16");
```

Это специализированный контейнер для PostgreSQL.


---
### Пример интеграционного теста

```java
@SpringBootTest
@Testcontainers
class UserRepositoryTest {

    @Container
    static PostgreSQLContainer<?> postgres =
            new PostgreSQLContainer<>("postgres:16")
                    .withDatabaseName("test_db")
                    .withUsername("test")
                    .withPassword("test");

    @DynamicPropertySource
    static void configure(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }

    @Autowired
    private UserRepository userRepository;

    @Test
    void shouldSaveUser() {
        // test
    }
}
```

---

# 5. Что здесь происходит

## `@Testcontainers`

Включает интеграцию Testcontainers + JUnit 5.

---

## `@Container`

Помечает контейнер, который:

- автоматически стартует
    
- автоматически остановится после тестов
    

---

## `static PostgreSQLContainer`

Контейнер часто делают static:

- поднимается 1 раз на класс
    
- тесты работают быстрее
    

Если убрать static:

- контейнер будет создаваться на каждый тест
    

---

# 6. Главный механизм подключения — `@DynamicPropertySource`

Это ключевой механизм интеграции со Spring Boot.

```java
@DynamicPropertySource
static void configure(DynamicPropertyRegistry registry)
```

Позволяет динамически зарегистрировать свойства datasource.

По сути:

```java
spring.datasource.url=
spring.datasource.username=
spring.datasource.password=
```

подставляются автоматически из контейнера.

---

# 7. Какие основные бины участвуют

Spring Boot сам создаёт почти всё автоматически.

---

## DataSource

Главный бин подключения к БД.

Создаётся автоконфигурацией Spring Boot:

```java
DataSource
```

Он использует:

- URL
    
- username
    
- password
    

которые мы подставили через `DynamicPropertyRegistry`.

---

## EntityManagerFactory

Если используется JPA/Hibernate:

```java
LocalContainerEntityManagerFactoryBean
```

Создаётся автоматически.

---

## TransactionManager

Для `@Transactional`:

```java
PlatformTransactionManager
```

обычно:

```java
JpaTransactionManager
```

---

## JdbcTemplate

Если есть JDBC starter:

```java
JdbcTemplate
```

---

## Flyway / Liquibase

Если они подключены:

- миграции автоматически применятся к Testcontainer БД при старте контекста
    

Это огромный плюс Testcontainers.

---

# 8. Что обычно НЕ нужно регистрировать вручную

Обычно НЕ создают вручную:

- DataSource
    
- EntityManagerFactory
    
- TransactionManager
    

Spring Boot делает это автоматически.

Ты только:

1. поднимаешь контейнер
    
2. подставляешь datasource properties
    

---

# 9. Альтернатива — `application-test.yml`

Иногда делают так:

```yaml
spring:
  datasource:
    url: jdbc:tc:postgresql:16:///testdb
```

Тогда контейнер поднимется автоматически через special JDBC driver.

Но этот способ:

- менее гибкий
    
- хуже контролируется
    
- реже используется в крупных проектах
    

Чаще используют именно `PostgreSQLContainer`.

---

# 10. Переиспользуемый Base Test Class

Частая практика:

```java
@Testcontainers
public abstract class AbstractIntegrationTest {

    @Container
    static PostgreSQLContainer<?> postgres =
            new PostgreSQLContainer<>("postgres:16");

    @DynamicPropertySource
    static void configure(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }
}
```

Потом:

```java
class UserRepositoryTest extends AbstractIntegrationTest {
}
```

---

# 11. `@ServiceConnection` — современный Spring Boot 3.1+ способ

В новых версиях Spring Boot есть встроенная интеграция.

```java
@Testcontainers
@SpringBootTest
class Test {

    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres =
            new PostgreSQLContainer<>("postgres:16");
}
```

И ВСЁ.

Spring Boot сам:

- создаст datasource
    
- подключит Hibernate
    
- зарегистрирует connection properties
    

Без `DynamicPropertySource`.

---

# 12. Что реально важно понимать

Testcontainers — это не замена Spring Data/JPA.

Он только:

- поднимает инфраструктуру
    
- даёт connection info
    

А Spring Boot уже:

- создаёт DataSource
    
- Hibernate
    
- TransactionManager
    
- repositories
    

---

# 13. Что чаще всего используют на практике

Сейчас стандарт де-факто:

```java
@SpringBootTest
@Testcontainers
```

```java
@PostgreSQLContainer
```

```java
@ServiceConnection
```

если Spring Boot 3.1+.

---

# 14. Типичная архитектура integration tests

```text
JUnit
  ↓
Testcontainers
  ↓
Docker PostgreSQL
  ↓
Spring Boot DataSource
  ↓
Hibernate/JPA
  ↓
Repository/Service tests
```

---

# 15. Когда Testcontainers особенно полезен

Он отлично ловит проблемы, которые H2 не покажет:

- PostgreSQL-specific SQL
    
- JSONB
    
- native queries
    
- locking
    
- transactions
    
- indexing
    
- migrations
    
- isolation levels
    
- optimistic/pessimistic locking
    
- constraint violations
    
- sequence behavior
    
- real query plans



----
#### [[Overview - Postgres Testcontainer - Flashcards|Link to flashcards]]



---
### References:

