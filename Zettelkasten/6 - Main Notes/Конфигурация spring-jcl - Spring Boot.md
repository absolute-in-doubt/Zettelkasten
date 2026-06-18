
11-01-2026 11:41

Status:

Tags: [[Spring]]

---
# Конфигурация spring-jcl - Spring Boot


> [!note]
> Конфигурация spring jcl ограничена. Например, в ней нельзя создать cusotm appenders, указать им имена и указать классы их реализации.
> Для более точечной конфигурации ипользуются config файлы реализаций, например `logback-spring.xml`


### Простая конфигурация (application.properties)


#### Пример установки уровней логгирования:

```properties
logging.level.org.springframework.web=debug
logging.level.org.hibernate=error
```

В application.yaml:

```yaml
logging:
  level:
    org.springframework.web: "debug"
    org.hibernate: "error"
```


#### Пример установки outut file:

(в него будет логироваться в дополнение к консоли)

```properties
logging.file.name=mylogfile.txt
```


#### Пример установки кастомного logging config file:

> [!note] 
> По умолчанию в приоритете является config file реализации.
> Т.е., если используем logback -> в приоритете config file `logback.xml`.
> 
> Searched by: `classpath:logback.xml`

Локацию конфигурационного файла логгирования можно изменить в application.properties:

```properties
logging.config=classpath:myconfig.properties
```


---
### Настройка pattern для appender

```yaml
logging:
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n"
    file: "%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n"
```


----
#### [[Конфигурация spring-jcl - Spring Boot - Flashcards|Link to flashcards]]



---
### References:

