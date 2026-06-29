
13-05-2026 17:33

Status:

Tags: [[Spring Boot]] [[Spring]]

---
# Автоконфигурация & ConditionalBeans - Spring Boot



**Автоконфигурация Spring Boot** автоматически создаёт и настраивает бины на основе:

1. зависимостей в classpath (`@ConditionalOnClass`);
    
2. уже существующих бинов (`@ConditionalOnMissingBean`);
    
3. свойств из `application.yml/properties` (`@ConditionalOnProperty`).
    

Автоконфигурации подключаются через `@EnableAutoConfiguration` (входит в `@SpringBootApplication`) и загружаются из `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`.

**Кратко:** _Spring Boot анализирует окружение приложения и автоматически регистрирует подходящие бины, если разработчик не определил их самостоятельно._


![[Pasted image 20260513173314.png]]


----
#### [[Автоконфигурация & ConditionalBeans - Spring Boot - Flashcards|Link to flashcards]]



---
### References:

