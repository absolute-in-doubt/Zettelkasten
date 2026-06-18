
07-01-2026 11:29

Status:

Tags: [[Testing]] [[Spring]]

---
# JUnit with Spring

При работе с чистым Spring для того, чтобы при запуске тестов поднимался контекст используем аннотацию **~={purple}@SpringJUnitConfig=~**, которая находится в `org.springframework.test.context.junit.jupiter`.

- `@SpringJUnitConfig` подключает `SpringExtension` и говорит JUnit, что тест работает с Spring‑контекстом.

- `@ContextConfiguration(classes = SpringConfig.class)` говорит, какую конфигурацию использовать (Java‑класс с `@Configuration`, XML и т.п.).


~={purple}@SpringJUnitConfig  = @ExtendWith(`SpringExtension.class`) + @ContextConfiguration=~



### Пример использования нескольких конфигурационных классов Spring:

SpringConfig - конфигурация бинов для нормального запуска [[Профили - Spring|@Profile("dev")]]

TestConfig - тут конфигурация бинов для тестов (DataSource для H2) 

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.junit.jupiter.SpringJUnitConfig;

@SpringJUnitConfig(classes = { SpringConfig.class, TestConfig.class })
class UserDaoTest {

    @Autowired
    private UserDao userDao;

    @Test
    void testSomething() {
        // здесь доступны бины и из SpringConfig, и из TestConfig
    }
}

```

----
#### [[JUnit with Spring - Flashcards|Link to flashcards]]



---
### References:

