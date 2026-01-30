
Theory for the cards: [[Процесс инициализации ApplicationContext - Spring]]

FILE TAGS: spring

Q: Перечисли этапы инициализации контекста Spring.
A:    
1. **prepareRefresh()** ← **ПАРСИНГ КОНФИГУРАЦИИ**
	
```
1.1 Инициализация Environment
1.2 Активация профилей (spring.profiles.active)
1.3 Загрузка PropertySources (application.yml/properties)
1.4 Парсинг YAML/Properties → Environment
1.5 Вызов ContextRefreshListeners.onApplicationEvent()
```
	
Вызов ContextRefreshListeners.onApplicationEvent() - вызов обработчиков события **`ContextRefreshEvent`** 
	
---
2. **obtainFreshBeanFactory()**
	
```
2.1 Создание BeanFactory (DefaultListableBeanFactory)
2.2 Загрузка BeanDefinition из @Configuration/@ComponentScan/XML ...
2.3 Регистрация Spring Core бинов (environment, etc.)
```
	
---
3. **prepareBeanFactory(beanFactory)**
	
```
3.1 Регистрация дефолтных бинов (именно дефолтных BeanPostProcessor)
3.2 Добавление PropertyPlaceholderConfigurer
```
	
Пример дефолтных бинов постпроцессоров:
- `ApplicationContextAwareProcessor`, 
- `ApplicationEventPublisherAwareProcessor`, 
- `MessageSourceAwareProcessor`
	
> [!warning]
> `PropertyPlaceholderConfigurer` нужно самому добавлять в контекст:
>
> В XML:
> ```xml
> <!-- Обязательно! --> <context:property-placeholder location="classpath:app.properties"/>
> ```
>
> В Java-config:
> ```java
@Configuration
public class AppConfig {
>    
>    @Bean
>    public static PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer() {
>        PropertySourcesPlaceholderConfigurer configurer = new PropertySourcesPlaceholderConfigurer();
>        configurer.setLocation(new ClassPathResource("app.properties"));
>        configurer.setIgnoreUnresolvablePlaceholders(true);
>        return configurer;
>    }
>    
>    @Bean
>    @Value("${db.url}")
>    public DataSource dataSource() {
>        // ${db.url} работает!
>    }
>}
> ```
	
---
4. **postProcessBeanFactory(beanFactory)**
	
```
BeanDefinitionRegistryPostProcessor.onPostProcess()
```
	
---
5. **invokeBeanFactoryPostProcessors(beanFactory)** ← **PropertyPlaceholder разрезолв**
	
```
Замена ${...} плейсхолдеров в BeanDefinition
```
	
---
6. **registerBeanPostProcessors(beanFactory)**
	
```
BeanPostProcessor, BeanFactoryPostProcessor
```
	
---
7. **initMessageSource() → initializeBeanFactory() → etc.**
	
```
Создание singleton бинов по очереди
```
	
---
8. **finishBeanFactoryInitialization()**
	
```
8.1 doGetBean() / getSingleton() <- Создание бина 
	
8.2 Конструктор ← new MyBean() 
	
8.3 postProcessBeforeInitialization() ← Все BeanPostProcessor 
	   
8.4 Инициализация бина (последовательно): 
	- @PostConstruct 
	- InitializingBean.afterPropertiesSet() 
	- initMethod (из @Bean(initMethod="init")) 
	   
8.5 postProcessAfterInitialization()** ← Все BeanPostProcessor 
	
8.6 Бин готов → помещается в singleton cache
```
	
---
9. **finishRefresh()**
	
```
Публикация ивента ContextRefreshEvent
```
<!--ID: 1769522954851-->
