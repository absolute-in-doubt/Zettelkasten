
Theory for the cards: 

FILE TAGS: spring_core spring

Q: Расскажи о внутреннем устройстве и возможностях `ApplicationContext` (какиеинтерфейсы расширяет `BeanFactory`: какие возможности реализует).
A: `ApplicationContext` — это фактически **расширение `BeanFactory`**, которое добавляет почти весь "enterprise-функционал" Spring.
	
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
**1. BeanFactory**
	
Базовый IoC-контейнер. Позволяет получать бины из контекста, проверять содержится ли в контексте конкретный bean:
	
```java
UserService service =
    beanFactory.getBean(UserService.class);
	
beanFactory.containsBean("userService");
```
	
Главная задача:
	
- хранение BeanDefinition;
- создание бинов;
- dependency injection.
	
---
**2. ListableBeanFactory**
	
Добавляет возможность перечислять бины.
	
Например:
	
```java
context.getBeansOfType(UserService.class);
	
context.getBeanDefinitionNames();
```
	
В обычном `BeanFactory` такого нет.
	
---
**3. HierarchicalBeanFactory**
	
Поддержка родительских контекстов. Например:
	
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
**4. EnvironmentCapable**
	
Доступ к окружению приложения.
	
```java
Environment env =
        context.getEnvironment();
```
	
Работа с:
	
- application.properties;
- application.yml;
- переменными окружения;
- JVM properties;
- profiles.
    
---
**5. MessageSource**
	
Поддержка internationalization — это функциональность для поддержки нескольких языков в приложении, позволяющая отображать сообщения на разных языках в зависимости от локали пользователя.
	
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
**6. ApplicationEventPublisher**
	
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
**7. ResourcePatternResolver**
	
Работа с ресурсами.
	
Например:
	
```java
Resource resource =
    context.getResource(
        "classpath:data.txt"
    );
	
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
	
<!--ID: 1782052325101-->
