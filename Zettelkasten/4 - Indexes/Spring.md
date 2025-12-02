
### Spring Principles

1. [[Dependency Injection]]
2. [[Inversion of control]]
3. [[AOP - Spring]]


##### Configuration class summary
- [[Configuration class summary- Spring]]

#### Spring Core

[[Инициализация контекста через XML - Spring]]
	- [[Про использование BeanPostProcessor - Spring]]

config:
- [[Application Context - Spring]]
	- [[ApplicationContext.xml file - Spring]]
	- [[property file - Spring]]
	- [[yaml file - Spring]]
- [[Configuration via Annotations - Spring]] - про @Value тут
- [[Configuration via Java code - Spring]]

- [[Spring Bean]]
	- [[Bean scope - Spring]]
	- [[Bean Lifecycle - Spring]]
	- [[Bean factory method - Spring]]


- [[spring-orm package]]
	- [[Настройка Hibernate - spring-orm]]
	- [[Поддержка декларативных транзакций - Spring-orm]]
		- [[@Transactional на методах service - Spring]]

#### Spring MVC

[[ViewResolver- Spring MVC]]
[[Request Handling Workflow - Spring MVC]]

config:
- [[web.xml для Spring MVC]]
	- [[Конфигурация web.xml с помощью Java класса + Spring]] - настройка DispatcherServletIntializer
	
- [[ApplicationContextMVC.xml file - Spring]]
	- [[Configurating Spring MVC via Java code]]


- [[DispatcherServlet - Spring MVC]]
- [[Controller - Spring MVC]] - @Controller
	- [[Получение параметров запроса - Spring MVC]]
- [[Model - Spring MVC]]
- [[Filter - Spring]] - настройка DispatcherServletIntializer


- [[JDBC Template - Spring]]


### Spring Data JPA

~={pink}@CrudRepository - ????=~
	 @JpaRepository - расширяет @CrudRepository 
	
- [[setup - Spring Data]]
- [[@Repository - Spring Data]] - @Repository
	-  [[Custom repository methods - Spring Data ]]
- [[Service - Spring Data]] - @Service



> [!warning]
> (Controller, Service, Repository) - по сути, семантические аннотации для лучшей читаемости и разделения слоёв.
> 
> Такие аннотации называются стериотипными. И хранятся в `org.springframework.stereotype`


### Spring Web

- [[RestTemplate - Spring Web]]

- [[@ResponseBody - Spring Web]]
- [[@RestController - Spring Web]]
- [[@ExceptionHandler - Spring Web]]
- [[@RequestBody - Spring Web]]


### [[Spring Boot]]

- [[setup - Spring Boot]]
- [[Spring Boot starters]]
- [[application.properties - Spring Boot]]


### Spring Security

- [[Авторизация vs Аутентификация]]

### [[Thymeleaf - шаблонизатор]]


### Многопоточность в Spring:

- [[ThreadPoolTaskExecutor - Spring]]


### Notes

- [[Classpath - Spring]]