
11-01-2026 11:41

Status:

Tags:

---
# Конфигурация spring-jcl - Spring Boot


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


----
#### [[Конфигурация spring-jcl - Spring Boot - Flashcards|Link to flashcards]]



---
### References:

