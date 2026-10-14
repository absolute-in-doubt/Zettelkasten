
09-11-2025 09:28

Status:

Tags: [[Spring Boot]] [[Spring]]

---
# Spring Boot starters

Содержат в себе набор подходящих друг другу зависимостей.

---
### Spring-boot-parent-starter

Проект, созданный через Spring Initializr будет также иметь spring-boot-parent-starter:

![[Pasted image 20260513173141.png]]


`spring-boot-starter-parent` — это **базовый каркас для Maven-проекта на Spring Boot**, который:

- задаёт проверенные версии зависимостей,
    
- конфигурирует плагины,
    
- упрощает сборку и запуск приложения.
    

Если убрать его, придётся вручную прописывать версии библиотек и плагины, что усложнит поддержку проекта.

Пример pom.xml:
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.5.7</version>
    <relativePath/> <!-- искать в репозитории, а не локально -->
</parent>

<properties>
    <java.version>17</java.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```


---
### Как создать собственный Spring Boot Starter

Для собственного **Spring Boot Starter** обычно нужны 2 части:

1. **Starter-модуль** — `pom.xml`/`build.gradle` с зависимостями, которые должен получить пользователь.
    
2. **Auto-Configuration-модуль** — классы с `@AutoConfiguration`, которые автоматически создают нужные beans и настраивают их.
    

Минимальная структура:

```text
my-starter/
├── pom.xml
└── src/main/java/
    └── MyAutoConfiguration.java
```

```java
@AutoConfiguration
@ConditionalOnMissingBean(MyService.class)
public class MyAutoConfiguration {

    @Bean
    MyService myService() {
        return new MyService();
    }
}
```

И зарегистрировать auto-configuration в:

```text
META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
```

В этом файле прописываем:

```text
com.example.MyAutoConfiguration
```

**Важно:** в современном Spring Boot `@AutoConfiguration` + `AutoConfiguration.imports` — основной механизм. Сам starter обычно является **тонким модулем с зависимостями**, а реальная логика находится в auto-configuration.

Если совсем кратко для интервью: **Starter подтягивает зависимости → AutoConfiguration при наличии этих зависимостей автоматически создаёт и настраивает beans.**


---
### Отключение определенных автоконфигураций

Отключение автоконфигураций - задача приложения, которрое импортирует зависимость, а не самого стартера:

- Через [[@SpringBootApplication - Spring Boot|@SpringBootApplication]](`exclude = {...}`) или `spring.autoconfigure.exclude` можно исключить конкретные auto-configuration.
    
```yaml
spring:
  autoconfigure:
    exclude:
      - com.mycustomstarter.configurations.FirstAutoConfiguration
      - com.mycustomstarter.configurations.SecondAutoConfiguration
```

----
#### [[Spring Boot starters - Flashcards|Link to flashcards]]



---
### References:

