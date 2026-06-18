
08-04-2026 19:07

Status:

Tags: [[Spring Core]] [[Spring]]

---
# @DependsOn - Spring Core

Входит в **Spring Context** (`spring-context`), пакет `org.springframework.context.annotation.DependsOn`.


Аннотация `@DependsOn` в Spring используется для явного указания зависимостей инициализации между бинами. Она гарантирует, что указанные бины будут созданы и полностью инициализированы до создания бина с этой аннотацией.

#### В каких случаях используется

`@DependsOn` нужна в случаях, когда бины не связаны через прямую инъекцию зависимостей (например, `@Autowired`), но один бин полагается на побочные эффекты другого — статические инициализаторы, регистрацию драйверов БД или кэш.


**Типичный сценарий**: регистрация JDBC-драйвера или инициализация кэша перед созданием репозиториев.


### Пример использования

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

### Ключевые особенности

- Применяется к классам с `@Component`/`@Service` или методам `@Bean`.
    
- Несколько зависимостей: `@DependsOn({"bean1", "bean2"})`.
    
- Циклические зависимости с `@DependsOn` приводят к ошибке.
    
- В XML-конфигурации аналог — атрибут `depends-on`.



----
#### [[@DependsOn - Spring Core - Flashcards|Link to flashcards]]



---
### References:

