
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
<!--ID: 1769523506919-->






