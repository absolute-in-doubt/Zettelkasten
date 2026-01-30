
### Spring Principles

1. [[Dependency Injection]]
2. [[Inversion of control]]



##### Configuration class summary
- [[Configuration class summary- Spring]]


### Spring Context

##### Многопоточность в Spring:
- [[ThreadPoolTaskExecutor - Spring]]


#### Spring Core

[[ЖЦ контекста - Spring]]

[[Процесс инициализации ApplicationContext - Spring]]
	- [[Подробнее про BeanDefinitionReaders - Spring]]
[[ClasspathXmlApplicationContext - Spring]]   - initing context with XML
[[AnnotationConfigApplicationContext - Spring]] - with annotations
	- [[Про использование BeanPostProcessor - Spring]]
		- [[Проксирование бина в BeanPostProcessor - Spring]]
	- [[Спринговские BeanPostProcessors - Spring]]
Spring Events:
[[ApplicationListener - Spring]]

Context debugging methods:
[[SpringContext helpful debugging methods - Spring]]


[[Пакеты Spring Core]]
[[Профили - Spring]]

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

[[@ComponentScan - Spring]]

### Spring ORM

- [[spring-orm package]]
	- [[Настройка Hibernate - spring-orm]]
	- @Transactional:
	- [[Поддержка декларативных транзакций - Spring-orm]] 
		- [[@Transactional - Jakarta & Spring ORM]]
		- [[@Transactional на методах service - Spring]] 
		- [[@Lock - optimistic & pessimistic Spring-orm]]

#### Spring Web MVC

[[WebApplicationContext - Spring MVC]]
	- [[Конфигурация WebApplicationContext - Spring MVC]]
	- [[@EnableWebMvc - Spring MVC]]

Инфраструктура Spring MVC
- [[DispatcherServlet - Spring Web MVC]]

[[ViewResolver- Spring Web MVC]]
[[Request Handling Workflow - Spring Web MVC]]

config:
- [[web.xml для Spring MVC]]
	- [[Конфигурация web.xml с помощью Java класса - Spring]] - настройка DispatcherServletIntializer
	- [[Добавление статических ресурсов в WebApplicationContext - Spring]]
	
- [[ApplicationContextMVC.xml file - Spring Web MVC]]
	- [[Configurating Spring MVC via Java code]]


- [[DispatcherServlet - Spring Web MVC]]
- [[Controller - Spring Web MVC]] - @Controller
	- [[Получение параметров запроса - Spring Web MVC]]
	- [[Редирект - Spring MVC]]
- [[Model - Spring MVC]]

- [[@ResponseBody - Spring Web MVC]]
- [[@RestController - Spring Web MVC]]
- [[@ExceptionHandler - Spring Web MVC]]
- [[@ResponseStatus - Spring Web MVC]]


- [[Редирект - Spring Web MVC]]
- [[Заголовки - Spring Web MVC]]
- [[Cookie - Spring Web MVC]]

- [[Filter - jakarta.servlet]] - настройка DispatcherServletIntializer
- [[FilterChain - jakarta.servlet]] 
	- [[Регистрация фильтров в FilterChain - jakarta.servlet]]
	- [[Регистрация фильтров в FilterChain - Spring]]


### Spring JDBC

- [[JDBC Template - Spring]]


### Spring Data JPA

~={pink}CrudRepository - ????=~
	 JpaRepository - расширяет CrudRepository 
	
- [[setup - Spring Data]]
- [[@Repository - Spring Data]] - @Repository
	-  [[Custom repository methods - Spring Data ]]
- [[Service - Spring Data]] - @Service



> [!warning]
> (Controller, Service, Repository) - по сути, семантические аннотации для лучшей читаемости и разделения слоёв.
> 
> Такие аннотации называются стериотипными. И хранятся в `org.springframework.stereotype`


### Spring Web

`spring-web` — более базовый модуль для работы с вебом, **без** MVC‑части.

[[Возможности Spring Web]]

- [[RestTemplate - Spring Web]]

- [[@RequestBody - Spring Web]]




### [[Spring Boot]]

- [[setup - Spring Boot]]
- [[Spring Boot starters]]
- [[application.properties - Spring Boot]]

- [[CommandLineRunner и ApplicationRunner- Spring Boot]]


### Spring Security

- [[Авторизация vs Аутентификация]]
- [[Spring Security Filters]]
- [[AuthenticationProvider - Spring Security]]

HttpSession vs Cookie [[HttpSession vs Cookie - Java servlet API]]

- [[config - Spring Security]]

- [[Filter - jakarta.servlet]]
- [[FilterChain - jakarta.servlet]] 
	- [[Регистрация фильтров в FilterChain - jakarta.servlet]]
	- [[Регистрация фильтров в FilterChain - Spring]]
- DelegatingFilterProxy
- 
- [[Security Filter Chain - Spring Security]]
- [[Custom Security Filter - Spring Security]]
	- [[Adding Custom Security FIlter - Spring Security]]

- [[Работа с JWT - Spring Security]]



### Spring Test

- [[Возможности Spring Test]]
- [[JUnit with Spring]]


### Spring AOP

- [[Возможности Spring AOP]]



### [[Logging in Spring + Spring Boot]]


### [[Thymeleaf - шаблонизатор]]





### Notes

- [[Classpath - Spring]]