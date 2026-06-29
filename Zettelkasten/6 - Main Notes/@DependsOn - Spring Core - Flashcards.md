
Theory for the cards:  [[@DependsOn - Spring Core]]

FILE TAGS: spring_core

Q: Расскажи про аннотацию `DependsOn`.
A: `@DependsOn` нужна в случаях, когда бины не связаны через прямую инъекцию зависимостей (например, `@Autowired`), но один бин полагается на побочные эффекты другого — статические инициализаторы, регистрацию драйверов БД или кэш.
	
```java
@Component
@DependsOn({"dataSource", "cacheManager"})  // Эти бины инициализируются первыми
public class ReportGenerator {
    // Зависит от настроенного DataSource и CacheManager
}
	
@Bean
@DependsOn("reportGenerator")
public DatabaseInitializer initializer() {
    return new DatabaseInitializer();
}
```
<!--ID: 1782054892270-->