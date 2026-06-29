
Theory for the cards: [[BFPP vs BPP - Spring Core]]

FILE TAGS: spring_core spring

Q: Чем BFPP отличается от BPP?
A: **BeanFactoryPostProcessor настраивает контейнер и BeanDefinition, а BeanPostProcessor настраивает и оборачивает уже созданные объекты бинов.**
	
![[Pasted image 20260621175540.png]]
<!--ID: 1782053751085-->

Q: Приведи примеры BFPP и BPP.
A: Известные реализации BFPP
	
**PropertySourcesPlaceholderConfigurer**
	
Обрабатывает:
	
```java
@Value("${db.url}")
```
	
---
**ConfigurationClassPostProcessor**
	
Одна из самых важных частей Spring.
	
Обрабатывает:
	
```java
@Configuration
@ComponentScan
@Bean
@Import
```
	
Фактически весь Java Config работает благодаря этому BFPP.
	
	
----
**Известные реализации BPP**
	
	
**AutowiredAnnotationBeanPostProcessor**
	
Обрабатывает:
	
```java
@Autowired
@Inject
@Value
```
	
---
**CommonAnnotationBeanPostProcessor**
	
Обрабатывает:
	
```java
@PostConstruct
@Resource
@PreDestroy
```
	
---
**AnnotationAwareAspectJAutoProxyCreator**
	
Создает AOP-прокси:
	
```java
@Transactional
@Async
@Cacheable
```
<!--ID: 1782053888666-->

