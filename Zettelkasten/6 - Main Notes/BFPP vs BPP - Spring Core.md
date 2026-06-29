
04-06-2026 10:16

Status:

Tags: [[Spring Core]] [[Spring]]

---
# BFPP vs BPP - Spring Core


- **BeanFactoryPostProcessor (BFPP)** работает с **метаданными бинов (`BeanDefinition`) до создания объектов**.
    
- **BeanPostProcessor (BPP)** работает с **уже созданными объектами бинов**.
    

---

# Общая картина

Во время старта контекста происходит примерно следующее:

```text
1. Загрузка BeanDefinition
2. BeanFactoryPostProcessor
3. Регистрация BeanPostProcessor
4. Создание singleton beans
5. BeanPostProcessor
```

Схематично:

```text
BeanDefinition
      │
      ▼
BeanFactoryPostProcessor
      │
      ▼
модифицированный BeanDefinition
      │
      ▼
создание объекта
      │
      ▼
BeanPostProcessor
      │
      ▼
готовый бин
```


---
# BeanFactoryPostProcessor

Интерфейс:

```java
public interface BeanFactoryPostProcessor {

    void postProcessBeanFactory(
        ConfigurableListableBeanFactory beanFactory
    );
}
```

Вызывается **до создания обычных бинов**.

На этом этапе объектов еще может не существовать.

Есть только:

```java
BeanDefinition
```

например:

```java
BeanDefinition {
    beanClass = UserService.class
    scope = singleton
}
```

---

## Что можно делать?

Изменять определения бинов.

Например:

```java
@Component
public class MyBFPP
        implements BeanFactoryPostProcessor {

    @Override
    public void postProcessBeanFactory(
            ConfigurableListableBeanFactory bf) {

        BeanDefinition bd =
            bf.getBeanDefinition("userService");

        bd.setScope("prototype");
    }
}
```

В результате:

```java
@Service
public class UserService {
}
```

станет prototype-бином.

---

## Известные реализации BFPP

### PropertySourcesPlaceholderConfigurer

Обрабатывает:

```java
@Value("${db.url}")
```

---

### ConfigurationClassPostProcessor

Одна из самых важных частей Spring.

Обрабатывает:

```java
@Configuration
@ComponentScan
@Bean
@Import
```

Фактически весь Java Config работает благодаря этому BFPP.

---

# BeanPostProcessor

Интерфейс:

```java
public interface BeanPostProcessor {

    Object postProcessBeforeInitialization(
        Object bean,
        String beanName);

    Object postProcessAfterInitialization(
        Object bean,
        String beanName);
}
```

Работает уже с объектом.

---

Например:

```java
@Service
public class UserService {
}
```

Spring создает объект:

```java
new UserService()
```

и затем передает его через все BPP.

---

## Что можно делать?

### Менять бин

```java
@Override
public Object postProcessAfterInitialization(
        Object bean,
        String beanName) {

    return proxy(bean);
}
```

---

### Создавать прокси

Именно так работают:

```java
@Transactional
@Async
@Cacheable
```

---

Например:

```java
@Service
@Transactional
public class PaymentService {
}
```

Реальный объект:

```java
PaymentService
```

заменяется на:

```java
PaymentService$$SpringProxy
```

через BPP.

---

# Известные реализации BPP

### AutowiredAnnotationBeanPostProcessor

Обрабатывает:

```java
@Autowired
@Inject
@Value
```

---

### CommonAnnotationBeanPostProcessor

Обрабатывает:

```java
@PostConstruct
@Resource
@PreDestroy
```

---

### AnnotationAwareAspectJAutoProxyCreator

Создает AOP-прокси:

```java
@Transactional
@Async
@Cacheable
```

---

# Самое важное различие

Представим бин:

```java
@Service
public class UserService {
}
```

До создания объекта существует только:

```java
BeanDefinition
```

BFPP работает здесь:

```text
BeanDefinition
     ▲
     │ BFPP
     ▼
```

После создания объекта:

```java
UserService service =
    new UserService();
```

работает BPP:

```text
UserService instance
      ▲
      │ BPP
      ▼
```

---

# Почему BPP не может менять scope?

Потому что поздно.

Когда вызывается BPP:

```java
postProcessAfterInitialization(...)
```

объект уже создан.

Если бин уже создан как singleton:

```java
new UserService()
```

то превратить его в prototype невозможно.

---

# Почему BFPP не может создавать прокси?

Потому что объекта еще нет.

Есть только описание:

```java
BeanDefinition
```

а проксировать нужно реальный экземпляр.

---

# Особый случай: BeanDefinitionRegistryPostProcessor

Это расширение BFPP.

```java
public interface BeanDefinitionRegistryPostProcessor
        extends BeanFactoryPostProcessor
```

Позволяет не только менять существующие определения, но и регистрировать новые.

Например:

```java
registry.registerBeanDefinition(...)
```

Именно так работает:

```java
@ComponentScan
```

через класс `ConfigurationClassPostProcessor`.

---

# Запомнить можно так


|                         | BeanFactoryPostProcessor                                                  | BeanPostProcessor                                                                |
| ----------------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Работает с              | `BeanDefinition`                                                          | объектом бина                                                                    |
| Когда                   | до создания бина                                                          | после создания бина                                                              |
| Может менять scope      | ✅                                                                         | ❌                                                                                |
| Может менять класс бина | ✅                                                                         | ❌ (слишком поздно)                                                               |
| Может создавать прокси  | ❌                                                                         | ✅                                                                                |
| Используется для        | конфигурации контейнера                                                   | модификации экземпляров                                                          |
| Примеры                 | `ConfigurationClassPostProcessor`, `PropertySourcesPlaceholderConfigurer` | `AutowiredAnnotationBeanPostProcessor`, `AnnotationAwareAspectJAutoProxyCreator` |


Одной фразой:

> **BeanFactoryPostProcessor настраивает контейнер и BeanDefinition, а BeanPostProcessor настраивает и оборачивает уже созданные объекты бинов.**


----
#### [[BFPP vs BPP - Spring Core - Flashcards|Link to flashcards]]



---
### References:

