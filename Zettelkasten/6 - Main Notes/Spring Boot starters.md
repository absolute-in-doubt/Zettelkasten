
09-11-2025 09:28

Status:

Tags: [[Spring]]

---
# Spring Boot starters

Содержат в себе набор подходящих друг другу зависимостей.

---
### Spring-boot-parent-starter

Проект, созданный через Spring Initializr будет также иметь spring-boot-parent-starter:

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


----
#### [[Spring Boot starters - Flashcards|Link to flashcards]]



---
### References:

