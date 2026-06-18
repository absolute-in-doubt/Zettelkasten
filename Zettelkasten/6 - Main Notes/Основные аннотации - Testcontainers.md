
25-05-2026 12:28

Status:

Tags: [[Testcontainers]]

---
# Основные аннотации - Testcontainers



##  Основные аннотации

#### ~={purple}@Testcontainers=~

- **Назначение:** Помечает класс или метод как требующий запуска контейнеров.
- **Использование:**
  ```java
  @Testcontainers
  public class MyIntegrationTest {
      // ...
  }
  ```
- **Особенности:**
  - Если аннотация стоит на классе, контейнеры запускаются **один раз на класс** (если не указано иное).
  - Если на методе — контейнеры запускаются **для каждого теста**.

---

### ~={purple}@Container=~

- **Назначение:** Помечает поле как контейнер, который нужно запустить.
- **Использование:**
  ```java
  @Testcontainers
  public class MyTest {
      @Container
      public PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:13");
  }
  ```
- **Особенности:**
  - Контейнер будет автоматически запущен перед тестом и остановлен после.
  - Поддерживает любые классы, наследуемые от `GenericContainer`.

---

### ~={purple}@ServiceConnection=~

- **Назначение:** Автоматически настраивает соединение с контейнером для Spring Boot тестов (начиная с **Testcontainers 1.17.0**).
- **Использование:**
  ```java
  @Testcontainers
  @SpringBootTest
  public class MySpringTest {
      @Container
      @ServiceConnection
      static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:13");
  }
  ```
- **Особенности:**
  - Автоматически подменяет свойства Spring (например, `spring.datasource.url`, `spring.datasource.username`, `spring.datasource.password`) на параметры контейнера.
  - Работает только с **Spring Boot 3.1.0+**.

---

### ~={purple}@Slf4jTestcontainers=~

- **Назначение:** Включает логгирование для контейнеров (использует SLF4J).
- **Использование:**
  ```java
  @Testcontainers
  @Slf4jTestcontainers
  public class MyTest {
      @Container
      public PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:13");
  }
  ```
- **Особенности:**
  - Логи контейнеров будут выводиться в консоль через SLF4J.

---

### ~={purple}@WaitFor=~

- **Назначение:** Указывает условие ожидания готовности контейнера.
- **Использование:**
  ```java
  @Container
  @WaitFor("waitForLogMessage(\"Database ready\", 1)")
  public PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:13");
  ```
- **Особенности:**
  - Поддерживает методы: `waitForLogMessage`, `waitForHttp`, `waitForListeningPort`, и др.
  - Можно использовать кастомные условия через реализацию интерфейса `WaitStrategy`.

---

## Примеры использования

### **Пример 1: Простой тест с PostgreSQL**

```java
@Testcontainers
public class PostgreSQLTest {
    @Container
    public PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:13");

    @Test
    public void testConnection() throws SQLException {
        String jdbcUrl = postgres.getJdbcUrl();
        String username = postgres.getUsername();
        String password = postgres.getPassword();

        try (Connection conn = DriverManager.getConnection(jdbcUrl, username, password)) {
            assertTrue(conn.isValid(1));
        }
    }
}
```

### **Пример 2: Тест с Spring Boot и @ServiceConnection**

```java
@Testcontainers
@SpringBootTest
public class SpringDataJpaTest {
    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:13");

    @Autowired
    private DataSource dataSource;

    @Test
    public void testDataSource() throws SQLException {
        try (Connection conn = dataSource.getConnection()) {
            assertTrue(conn.isValid(1));
        }
    }
}
```

### **Пример 3: Кастомный WaitStrategy**

```java
@Testcontainers
public class CustomWaitTest {
    @Container
    public GenericContainer<?> redis = new GenericContainer<>("redis:6")
        .withExposedPorts(6379)
        .waitingFor(Wait.forListeningPort());

    @Test
    public void testRedisConnection() {
        String host = redis.getHost();
        int port = redis.getFirstMappedPort();
        // Подключение к Redis...
    }
}
```

---

## **Лучшие практики**

1. **Используйте статические контейнеры** для ускорения тестов (запуск один раз на класс):
  ```java
   @Container
   static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:13");
  ```
2. **Настраивайте таймауты** для ожидания готовности контейнера:
  ```java
   .withStartupTimeout(Duration.ofSeconds(60))
  ```
3. **Используйте @ServiceConnection** для интеграции с Spring Boot.
4. **Логгируйте контейнеры** с помощью `@Slf4jTestcontainers` для отладки.
5. **Очищайте данные** между тестами, если контейнер используется повторно.


----
#### [[Основные аннотации - Testcontainers - Flashcards|Link to flashcards]]



---
### References:

