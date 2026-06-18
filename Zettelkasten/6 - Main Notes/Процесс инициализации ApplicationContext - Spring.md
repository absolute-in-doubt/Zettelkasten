
24-01-2026 09:23

Status:

Tags: [[Spring]]

---
# Процесс инициализации ApplicationContext - Spring

Всего есть 4 способа сконфигурировать контекст:

1. **Xml конфигурация** — ClassPathXmlApplicationContext(”context.xml”);

2. **Groovy конфигурация** — GenericGroovyApplicationContext(”context.groovy”);

3. **Конфигурация через аннотации с указанием пакета для сканирования** — AnnotationConfigApplicationContext(”package.name”);

4. **JavaConfig** — конфигурация через аннотации с указанием класса (или массива классов) помеченного аннотацией @Configuration — AnnotationConfigApplicationContext(JavaConfig.class).


---

### Этапы загрузки контекста (AbstractApplicationContext.refresh())

~={orange}Все следующие методы - `private` методы `AbstractApplicationContext`. Они вызываются **последовательно методом `refresh()`** того же класса при загрузке Spring контекста.=~


![[Pasted image 20260127155511.png]]

![[Pasted image 20260129101210.png]]

#### 1. **prepareRefresh()** ← **ПАРСИНГ КОНФИГУРАЦИИ**

```
1.1 Инициализация Environment
1.2 Активация профилей (spring.profiles.active)
1.3 Загрузка PropertySources (application.yml/properties)
1.4 Парсинг YAML/Properties → Environment
1.5 Вызов ContextRefreshListeners.onApplicationEvent()
```

Вызов ContextRefreshListeners.onApplicationEvent() - вызов обработчиков события **`ContextRefreshEvent`** (**[[ApplicationListener - Spring|Обработка ивентов]]**)


#### 2. **obtainFreshBeanFactory()**

```
2.1 Создание BeanFactory (DefaultListableBeanFactory)
2.2 Загрузка BeanDefinition из @Configuration/@ComponentScan/XML ...
2.3 Регистрация Spring Core бинов (environment, etc.)
```

![[Pasted image 20251101122152.png]]

2.2 **BeanDefinitionsReader** (стрекоза) читает отмеченные классы (аннотациями, в Java-class, XML - зависит от типа создаваемого контекста)
#### [[Подробнее про BeanDefinitionReaders - Spring ]]

В первую очередь создаёт бины, реализующие интерфейс **[[Про использование BeanPostProcessor - Spring|BeanPostProcessor]]** (и остальные бины для внутреннего использования) и складываются отдельно от остальных бинов.


#### 3. **prepareBeanFactory(beanFactory)**

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

#### 4. **postProcessBeanFactory(beanFactory)**

```
BeanDefinitionRegistryPostProcessor.onPostProcess()
```


#### 5. **invokeBeanFactoryPostProcessors(beanFactory)** ← **PropertyPlaceholder разрезолв**

```
Замена ${...} плейсхолдеров в BeanDefinition
```

> [!warning]
> - **PropertyPlaceholder (фаза 5)**: Замена плейсхолдеров **в метаданных BeanDefinition**. Например, `@Value("${app.name}")` → `@Value("myApp")`. Это происходит до создания бинов.​
>    
>- **@Value резолвинг (позже)**: Уже после создания экземпляра, в `populateBean()` через `AutowiredAnnotationBeanPostProcessor`. Здесь строка "myApp" преобразуется в реальное значение.


#### 6. **registerBeanPostProcessors(beanFactory)**

```
BeanPostProcessor, BeanFactoryPostProcessor
```


#### 7. **initMessageSource() → initializeBeanFactory() → etc.**

```
Создание singleton бинов по очереди
```


#### 8. **finishBeanFactoryInitialization()**

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


#### 9. **finishRefresh()**

```
Публикация ивента ContextRefreshEvent
```

**[[ApplicationListener - Spring|Обработка ивентов]]**

----
### TL;DR

```
SpringApplication.run() 
    ↓
ConfigDataEnvironmentPostProcessor  ← YAML/PROPERTIES ПАРСИНГ
    ↓
AnnotationConfigApplicationContext.refresh()
    ↓
1. prepareRefresh()                 ← Environment + Properties готов
    ↓
2. obtainFreshBeanFactory()         ← @Configuration → BeanDefinition
    ↓
3-7. Инициализация BeanFactory     ← ${} placeholders resolved
    ↓
3. finishBeanFactoryInitialization()
    ↓
4. finishRefresh()                 ← Контекст готов!

```



----
#### [[Процесс инициализации ApplicationContext - Spring - Flashcards|Link to flashcards]]



---
### References:

