
27-06-2026 16:32

Status:

Tags: [[Spring Data MongoDB]] [[Spring Data Access]] [[Spring]]

---
# Configuration - Spring Data MongoDB

Чтобы использовать  репозитории (была сконфигурирована реализация), используем ~={purple}**@EnableMongoRepositories**=~.

If no base package is configured, the infrastructure scans the package of the annotated configuration class.

Пример:

```java
@Configuration
@EnableMongoRepositories("com.acme..repositories")
class ApplicationConfig extends AbstractMongoClientConfiguration {

  @Override
  protected String getDatabaseName() {
    return "e-store";
  }

  @Override
  protected String getMappingBasePackage() {
    return "com.acme..repositories";
  }
}
```

This namespace element causes the base packages to be scanned for interfaces that extend `MongoRepository` and create Spring beans for each one found.





The `basePackages` and `value` attributes in `@EnableMongoRepositories` support `${…}` property placeholders which are resolved against the `Environment` as well as Ant-style package patterns such as `"org.example.**"`.


```java
@Configuration
@EnableMongoRepositories("${app.scan.packages}")
public class ApplicationConfiguration {
  // …
}
```


Фильтрация классов для сканирования - [тут](https://docs.spring.io/spring-data/mongodb/reference/repositories/create-instances.html#repositories.using-filters)

----
#### [[Configuration - Spring Data MongoDB - Flashcards|Link to flashcards]]



---
### References:

