
07-01-2026 09:43

Status:

Tags: [[Spring Core]] [[Spring]]

---
# Профили - Spring

Основные возможности профилей:​

- Условное создание бинов: бин/конфигурация помечаются `@Profile("dev")`, `@Profile("prod")` и загружаются только при активном соответствующем профиле.

- Разные настройки для разных окружений:
    
    - файлы `application-dev.properties`, `application-prod.yml` и т.п.;
        
    - разные реализации интерфейсов (например, мок‑реализация в `test`, реальная — в `prod`).
        
- Возможность одновременно активировать несколько профилей (например, `dev,local`), комбинируя конфигурации.
    
- Управление профилями через `spring.profiles.active` (properties, переменные окружения, параметры командной строки).
    

Это избавляет от «if‑ов по окружению» в коде и выносит различия в конфигурацию.


### Как установить профиль для запуска

1. **Через переменную окружения** — наиболее распространённый способ для Docker/Kubernetes/CI/CD:

```env
SPRING_PROFILES_ACTIVE=prod
```

2. **В `application.yml`/`application.properties`**:

```yaml
spring:  
	profiles:    
		active: dev
```

Но этот способ используют реже, поскольку он жёстко привязывает приложение к конкретному профилю.

Для продакшена профиль чаще всего задают **в окружении (environment variables) или через параметры запуска**, а не в коде и не в конфигурационных файлах. 


> Обычно активный Spring Profile задают вне приложения — через переменные окружения, JVM-параметры или аргументы командной строки. Это позволяет запускать один и тот же артефакт в разных окружениях без изменения кода.


### Доп способы настройки профилей


- Программно (main class) **Spring Boot**

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication app = new SpringApplication(Application.class);
        app.setAdditionalProfiles("prod", "dev");  // Добавляет к активным
        app.run(args);
    }
}
```


- Через System properties

```java
System.setProperty("spring.profiles.active", "prod");
SpringApplication.run(Application.class, args);
```


---
### Аннотация @Profile

Привязывает бины к конкретному профилю.

```java
@Service
@Profile("dev")
public class DevEmailService implements EmailService {

    @Override
    public void send(String to, String text) {
        System.out.println("DEV: письмо в лог: " + to + " -> " + text);
    }
}
```

```java
@Configuration
public class DataSourceConfig {

    @Bean
    @Profile("dev")
    public DataSource devDataSource() {
        BasicDataSource ds = new BasicDataSource();
        ds.setUrl("jdbc:h2:mem:devdb");
        ds.setUsername("dev");
        ds.setPassword("dev");
        return ds;
    }

    @Bean
    @Profile("prod")
    public DataSource prodDataSource() {
        BasicDataSource ds = new BasicDataSource();
        ds.setUrl("jdbc:postgresql://prod/db");
        ds.setUsername("prod");
        ds.setPassword("prod");
        return ds;
    }
}
```

```java
@Configuration
@Profile("test")
public class TestConfig {

    @Bean
    public DataSource testDataSource() {
        // in‑memory БД для тестов
    }
}
```

Вся конфигурация в этом классе будет подключена только при активном профиле `test`

> [!tip]
> Бин без аннотации `@Profile` не «относится» к какому‑то одному профилю — он создаётся **во всех** профилях, пока контекст в целом загружен. Активные профили ограничивают только те бины/конфигурации, которые явно помечены `@Profile(...)`; всё без `@Profile` всегда доступно.

---
## Аннотация @ActiveProfiles

`@ActiveProfiles` используется ~={orange}**на тестовых классах**=~, чтобы указать, какие профили должны быть активны при загрузке `ApplicationContext` для интеграционных тестов.​

```java
@ExtendWith(SpringExtension.class)
@SpringBootTest
@ActiveProfiles("test")
class MyServiceTest {
    // тесты с активным профилем "test"
}
```

```java
@ActiveProfiles({"dev", "integration"})
class DeveloperIntegrationTests {
    // тесты с профилями "dev" и "integration"
}
```
  
- Можно указывать несколько профилей и использовать резолвер (через `resolver = ...`), чтобы вычислять профили программно.
    
- Поддерживает наследование: профили, объявленные в базовом тестовом классе, могут переиспользоваться в наследниках.

----
#### [[Профили - Spring - Flashcards|Link to flashcards]]



---
### References:

