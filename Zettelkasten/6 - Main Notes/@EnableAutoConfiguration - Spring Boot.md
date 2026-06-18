
26-02-2026 13:31

Status:

Tags: [[Spring Boot]] [[Spring]]

---
# `@EnableAutoConfiguration`

- Включает механизм автоконфигурации Spring Boot.

- Boot сканирует `META-INF/spring.factories` (в Spring Boot 2) или `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` (в Spring Boot 3) и автоматически подключает классы‑конфигурации.
   
**Примеры:**
- если в classpath есть `spring-boot-starter-data-jpa`, то автоматически создаётся `EntityManagerFactory`, `DataSource`, `JpaTransactionManager` и т. д. 
	`+` выполняется [[setup - Spring Data|@EnableJpaRepositories]]

- если есть `spring-boot-starter-web`, автоматически выполняется инициализация DispatcherServlet
	`+` выполняется [[Configurating Spring MVC via Java code|@EnableWebMvc]]




---
### References:

