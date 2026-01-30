
07-01-2026 13:11

Status:

Tags: [[Spring]]

---
# @ComponentScan - Spring

Предназаначена для сканирования классов в директории и регистрации их как бинов.



---

Если `WebConfig` и контроллеры лежат, например, в пакете `com.example.web`, а ядро — в `com.example.core`, можно сделать так:

```java
@Configuration
@ComponentScan(
    basePackages = "com.example",
    excludeFilters = @ComponentScan.Filter(
        type = FilterType.REGEX,
        pattern = "com\\.example\\.web\\..*"
    )
)
public class SpringConfig {
}
```

Здесь `FilterType.REGEX` и `pattern` фактически исключают всё из `com.example.web` и его подпакетов.


### Исключение по классу

```java
@ComponentScan(
    basePackages = "com.example",
    excludeFilters = @ComponentScan.Filter(
        type = FilterType.ASSIGNABLE_TYPE,
        value = WebConfig.class
    )
)
```

### Исключение по аннотации

```java
@ComponentScan(
    basePackages = "com.example",
    excludeFilters = @ComponentScan.Filter(
        type = FilterType.ANNOTATION,
        classes = org.springframework.stereotype.Controller.class
    )
)
```


---
### References:

