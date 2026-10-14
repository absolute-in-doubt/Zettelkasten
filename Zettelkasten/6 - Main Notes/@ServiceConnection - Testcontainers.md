
23-09-2026 11:24

Status:

Tags: [[Testcontainers]] [[Testing]] [[Java+]]

---
# @ServiceConnection - Testcontainers


~={orange}При использовании `@ServiceConnection` в Spring Boot 3.1+ **вам не нужно вручную настраивать соединение** через `@DynamicPropertySource` или прописывать `spring.datasource.url/username/password` в `application-test.yaml` для тестового профиля — Spring Boot сам подставит эти свойства на основе контейнера.docs.=~

## Что делает `@ServiceConnection`

Когда вы аннотируете поле с контейнером:

```java
@SpringBootTest
@Testcontainers
class MyIntegrationTest {

    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres =
        new PostgreSQLContainer<>("postgres:16-alpine");

    // тесты...
}
```

Spring Boot:

1. Видит `@ServiceConnection` на поле типа `PostgreSQLContainer`.
    
2. Создаёт бин `JdbcConnectionDetails` (или более специфичный `PostgresConnectionDetails`).
    
3. Автоконтроллер `DataSourceAutoConfiguration` читает эти детали и **автоматически конфигурирует** `DataSource` (URL, username, password, driver).
    

Это заменяет старый паттерн:

```java
@DynamicPropertySource
static void configure(DynamicPropertyRegistry registry) {
    registry.add("spring.datasource.url", postgres::getJdbcUrl);
    registry.add("spring.datasource.username", postgres::getUsername);
    registry.add("spring.datasource.password", postgres::getPassword);
}
```

который теперь можно удалить.

(~={blue}**так-то его можно юзать для других настроек, но для datasource он больше не нужен**=~)

----
#### [[@ServiceConnection - Testcontainers - Flashcards|Link to flashcards]]



---
### References:

