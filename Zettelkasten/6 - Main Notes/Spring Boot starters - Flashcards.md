
Theory for the cards: [[Spring Boot starters]]

FILE TAGS: spring interview_fuckups java_interview

Q: Как создать собственный Spring Boot Starter
A: Для собственного **Spring Boot Starter** обычно нужны 3 части:
	
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
	
3. зарегистрировать auto-configuration в:
	
```text
META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
```
	
В этом файле прописываем:
	
```text
com.example.MyAutoConfiguration
```
	
**Важно:** в современном Spring Boot `@AutoConfiguration` + `AutoConfiguration.imports` — основной механизм. Сам starter обычно является **тонким модулем с зависимостями**, а реальная логика находится в auto-configuration.
<!--ID: 1790347764714-->
