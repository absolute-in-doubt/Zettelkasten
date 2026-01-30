
Theory for the cards: [[Профили - Spring]]

FILE TAGS: spring spring_core

Q: Какой бин здесь будет загружен , если не указать используемый профиль при запуске приложения?
A:   
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
A: Никакой не будет
<!--ID: 1769523286918-->

Q: Как мы обычно указываем, какой профиль использовать при запуске? Можно ли использовать сразу несколько профилей?
A:  Использовать можно несколько профилей. Указываем их через запятую.
	
1. В `application.properties`: 
	
```properties
spring.profiles.active=prod
```
	
2. Программно (main class) **Spring Boot**
	
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
	
	
3. Через System properties
	
```java
System.setProperty("spring.profiles.active", "prod");
SpringApplication.run(Application.class, args);
```
<!--ID: 1769523506919-->



