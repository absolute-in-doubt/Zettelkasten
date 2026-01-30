
07-01-2026 09:43

Status:

Tags: [[Spring]]

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


> [!warning] 
> Настройку профилей надо выполнить до загрузки контекста, поэтому, например в Spring Web MVC это делается в web.xml, или классе, который его заменяет.
> 
> Вот пример:
> ```java
> public class MySpringMVCDispatcherServletInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {  
  >
>    @Override  
>    public void onStartup(ServletContext servletContext) throws ServletException {  
>        // сначала вызываем стандартную инициализацию  
>        super.onStartup(servletContext);  
>  
>        // задаём активный профиль  
>        servletContext.setInitParameter("spring.profiles.active", "dev");  
>        // или несколько профилей:  
>        // servletContext.setInitParameter("spring.profiles.active", "dev,testdb");    }  
>      
>    @Override  
>    protected Class<?>[] getRootConfigClasses() {  
>        return null;  
>    }  
>  
>    @Override  
>    protected Class<?>[] getServletConfigClasses() {  
>        return new Class[] {SpringConfig.class};  
>    }  
>  
>    @Override  
>    protected String[] getServletMappings() {  
>        return new String[] {"/"};  
>    }  
>}
> ```
> 
> Пример записи в web.xml:
> ```xml
> <context-param>
>    <param-name>spring.profiles.active</param-name>
>    <param-value>dev</param-value>
></context-param>
> ```


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

`@ActiveProfiles` используется **на тестовых классах**, чтобы указать, какие профили должны быть активны при загрузке `ApplicationContext` для интеграционных тестов.​

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

