
Theory for the cards: [[Автоконфигурация & ConditionalBeans - Spring Boot]]

FILE TAGS: spring_boot spring

Q: Расскажи о том, как работает автоконфигурация в Spring Boot
A: **Автоконфигурация Spring Boot** автоматически создаёт и настраивает бины на основе:
	
1. зависимостей в classpath (`@ConditionalOnClass`);
    
2. уже существующих бинов (`@ConditionalOnMissingBean`);
    
3. свойств из `application.yml/properties` (`@ConditionalOnProperty`).
    
Автоконфигурации подключаются через `@EnableAutoConfiguration` (входит в `@SpringBootApplication`) и загружаются из `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`.
	
**Кратко:** _Spring Boot анализирует окружение приложения и автоматически регистрирует подходящие бины, если разработчик не определил их самостоятельно._
<!--ID: 1782052700824-->
