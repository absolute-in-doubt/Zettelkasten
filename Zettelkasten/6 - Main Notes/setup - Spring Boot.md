
03-11-2025 12:42

Status: #baby 

Tags: [[Spring]]

---
# setup - Spring Boot

~={purple}@SpringBootApplication=~ — это «комбинированная» аннотация, которая включает в себя три ключевых механизма Spring Boot: **автоконфигурацию (**~={purple}@EnableAutoConfiguration=~**)**, **сканирование компонентов (**`@ComponentScan`**)** и **регистрацию конфигурации (**~={purple}@SpringBootConfiguration=~**)**.


### `@SpringBootConfiguration`

- По сути, это разновидность ~={purple}@Configuration=~.

- Позволяет объявлять бины через `@Bean` и импортировать другие конфигурации.

----
### `@EnableAutoConfiguration`

- Включает механизм автоконфигурации Spring Boot.

- Boot сканирует `META-INF/spring.factories` (в Spring Boot 2) или `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` (в Spring Boot 3) и автоматически подключает классы‑конфигурации.
   
**Примеры: **
- если в classpath есть `spring-boot-starter-data-jpa`, то автоматически создаётся `EntityManagerFactory`, `DataSource`, `JpaTransactionManager` и т. д. 
	`+` выполняется [[setup - Spring Data|@EnableJpaRepositories]]

- если есть `spring-boot-starter-web`, автоматически выполняется инициализация DispatcherServlet
	`+` выполняется [[Configurating Spring MVC via Java code|@EnableWebMvc]]

----
## `@SpringBootApplication`

Содержит в себе:
- ~={purple}@SpringBootConfiguration=~  
- ~={purple}@EnableAutoConfiguration=~

- ~={purple}@ComponentScan=~ - настроен так, что рекурсивно  сканирует классы в текущей папке и подпапках:
![[Pasted image 20251109093940.png]]
Отсканирует папку `org.bsuir.spring.boot.app` (Где находдится только сам Application) + подпапки (controller, service (и его подпапки))

> [!warning] 
> Класс с аннотацией ~={purple}@SpringBootApplication=~ должен неаходиться в корне проекта

----
#### [[setup - Spring Boot - Flashcards|Link to flashcards]]



---
### References:

