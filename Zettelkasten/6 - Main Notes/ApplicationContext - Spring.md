
04-06-2026 09:58

Status:

Tags: [[Spring Core]] [[Spring]]

---
# ApplicationContext - Spring

`ApplicationContext` — это фактически **расширение `BeanFactory`**, которое добавляет почти весь "enterprise-функционал" Spring.

Если посмотреть на иерархию интерфейсов Spring 6:

```java
public interface ApplicationContext
    extends EnvironmentCapable,
            ListableBeanFactory,
            HierarchicalBeanFactory,
            MessageSource,
            ApplicationEventPublisher,
            ResourcePatternResolver
```

При этом:

```java
ListableBeanFactory extends BeanFactory
```

То есть `ApplicationContext` уже включает возможности `BeanFactory` и добавляет новые.

---

# 1. BeanFactory

Базовый IoC-контейнер.

Позволяет:

```java
UserService service =
    beanFactory.getBean(UserService.class);
```

или

```java
beanFactory.containsBean("userService");
```

Главная задача:

- хранение BeanDefinition;
    
- создание бинов;
    
- dependency injection.
    

---

# 2. ListableBeanFactory

Добавляет возможность перечислять бины.

Например:

```java
context.getBeansOfType(UserService.class);
```

или

```java
context.getBeanDefinitionNames();
```

В обычном `BeanFactory` такого нет.

---

# 3. HierarchicalBeanFactory

Поддержка родительских контекстов.

Например:

```text
Parent ApplicationContext
        ↑
Child ApplicationContext
```

Если бин не найден в дочернем контексте:

```java
childContext.getBean(...)
```

Spring поднимется вверх и попробует найти его в родительском.

Используется в:

- Spring MVC;
    
- Spring Boot;
    
- тестах;
    
- старых enterprise-приложениях.
    

---

# 4. EnvironmentCapable

Доступ к окружению приложения.

```java
Environment env =
        context.getEnvironment();
```

Дальше:

```java
env.getProperty("server.port");
```

или

```java
env.acceptsProfiles("prod");
```

Работа с:

- application.properties;
    
- application.yml;
    
- переменными окружения;
    
- JVM properties;
    
- profiles.
    

---

# 5. MessageSource

Поддержка i18n.

Например:

```java
context.getMessage(
    "error.user.notfound",
    null,
    Locale.US
);
```

Файлы:

```text
messages.properties
messages_ru.properties
messages_de.properties
```

---

# 6. ApplicationEventPublisher

Встроенная event-система Spring.

Публикация:

```java
context.publishEvent(
    new UserCreatedEvent(user)
);
```

Подписка:

```java
@EventListener
public void handle(UserCreatedEvent event) {
}
```

---

# 7. ResourcePatternResolver

Работа с ресурсами.

Например:

```java
Resource resource =
    context.getResource(
        "classpath:data.txt"
    );
```

или

```java
context.getResources(
    "classpath*:/**/*.xml"
);
```

Поддерживаются:

```text
classpath:
file:
http:
https:
```

---

# Что ещё даёт ApplicationContext по сравнению с BeanFactory?

Хотя это не видно напрямую из интерфейсов, Spring использует `ApplicationContext` для автоматического запуска инфраструктуры:

### BeanPostProcessor

```java
@Autowired
@Transactional
@Async
```

работают благодаря автоматически зарегистрированным post-processors.

---

### Application Events

```java
@EventListener
```

---

### AOP

```java
@Transactional
@Cacheable
@Async
```

---

### Lifecycle callbacks

```java
@PostConstruct
@PreDestroy
```

---

# Почему обычно используют ApplicationContext, а не BeanFactory?

Исторически:

```java
BeanFactory
```

был минимальным контейнером.

```java
ApplicationContext
```

— полноценным контейнером приложения.

На практике почти весь современный Spring-код использует именно `ApplicationContext`.

Например в Spring Boot:

```java
ConfigurableApplicationContext context =
        SpringApplication.run(...);
```

---

Можно представить иерархию так:

```text
BeanFactory
    │
    ├── получение бинов
    │
    ▼
ListableBeanFactory
    │
    ├── поиск бинов по типу
    │
    ▼
ApplicationContext
    │
    ├── Events
    ├── Resources
    ├── i18n
    ├── Profiles
    ├── Environment
    ├── AOP infrastructure
    ├── BeanPostProcessors
    └── Lifecycle management
```

Поэтому часто говорят:

> `BeanFactory` — это IoC-контейнер, а `ApplicationContext` — это полноценная платформа выполнения Spring-приложения поверх IoC-контейнера.


----
#### [[ApplicationContext - Spring - Flashcards|Link to flashcards]]



---
### References:

