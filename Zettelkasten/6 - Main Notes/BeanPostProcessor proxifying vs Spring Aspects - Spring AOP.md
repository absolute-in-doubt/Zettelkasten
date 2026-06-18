
22-04-2026 16:24

Status:

Tags: [[Spring]]

---
# BeanPostProcessor proxifying vs Spring Aspects - Spring AOP

| Характеристика         | Spring AOP                                                    | BeanPostProcessor                                                 |
| ---------------------- | ------------------------------------------------------------- | ----------------------------------------------------------------- |
| **Матчинг**            | Pointcut-выражения (`@annotation(Logged)`, `execution(*..*)`) | Ручная проверка в коде (`if (hasAnnotation(bean, Logged.class))`) |
| **Типы Advice**        | 5 видов: `@Before/@After/@Around`                             | Только `postProcessAfterInitialization` (одно место)              |
| **Гибкость**           | Множественные аспекты, порядок (`@Order`), композиция         | Один прокси на бин, сложнее комбинировать                         |
| **Читаемость**         | Декларативно: `@Logged` на методе                             | Императивно: логика в `postProcess`                               |
| **Производительность** | Оптимизированные pointcut'ы                                   | Ручная рефлексия каждый раз                                       |



# Главное различие

|Подход|Уровень работы|
|---|---|
|Spring AOP / Aspects|interception вызовов методов|
|BeanPostProcessor|работа с bean lifecycle|

---

Spring AOP работает `на уровне method invocation`

BeanPostProcessor работает `на уровне создания bean`


---
#### BeanPostProcessor не устарел

Наоборот `Spring AOP сам построен на BeanPostProcessor`.

например:

- `AnnotationAwareAspectJAutoProxyCreator`
    
- `InfrastructureAdvisorAutoProxyCreator`

это BPP.

> [!note] 
> Spring AOP тоже создаёт proxy для вызова метода, просто часть кода выполняется под капотом. И это даёт меньше гибкости: нельзя полностью заменить bean например.



---
# Что может Aspect

Aspect может получить почти всю runtime информацию через JoinPoint / ProceedingJoinPoint.

Можно получить:

- method
    
- args
    
- target bean
    
- proxy
    
- annotations
    
- return value
    
- exception
    

---

# Пример

```java
@Around("@annotation(Cacheable)")
public Object log(ProceedingJoinPoint pjp)
        throws Throwable {

    MethodSignature signature =
            (MethodSignature) pjp.getSignature();

    Method method = signature.getMethod();

    Object[] args = pjp.getArgs();

    Object target = pjp.getTarget();

    return pjp.proceed();
}
```

---

# Что Aspect НЕ может легко

Aspect не очень подходит для:

- bean definition manipulation
    
- lifecycle hooks
    
- replacing bean implementations
    
- low-level container infrastructure
    

---

# Что может BeanPostProcessor

BPP имеет доступ к:

- original bean
    
- bean class
    
- bean name
    
- lifecycle stages
    
- annotations before proxying
    

---

# Главное преимущество BPP

Можно `заменить bean целиком`.


---

# Use cases Aspect

## Хорошо подходит для cross-cutting concerns

- logging
    
- transactions
    
- security
    
- metrics
    
- retries
    
- caching
    

Потому что AOP работает вокруг method execution

---

# Use cases BeanPostProcessor

## Infrastructure / framework development

- custom proxy creation
    
- annotation processing
    
- automatic bean wrapping
    
- framework internals
    
- dependency injection extensions
    

---

### Через BPP работают

- `@Autowired`
    
- `@Transactional`
    
- `@Async`
    
- `@Cacheable`


---

### Важное различие

#### Aspect

Ты говоришь `что делать вокруг вызова метода`

#### BPP

Ты говоришь `как модифицировать bean при создании`

---

# Что проще использовать

|Задача|Лучше|
|---|---|
|logging|Aspect|
|retries|Aspect|
|transactions|Aspect|
|metrics|Aspect|
|custom framework|BPP|
|auto-proxying|BPP|
|bean replacement|BPP|
|annotation scanning|BPP|

---

# Можно ли в Aspect получить method metadata?

Да.

Через:

```java
MethodSignature
```

можно получить:

- return type
    
- generic types
    
- annotations
    
- parameter annotations
    
- parameter names
    

---

# Но есть отличие

Aspect видит:

```text
уже готовый bean/proxy
```

---

А BPP может работать:

```text
до proxy creation
```

---

# Архитектурно

## Aspect

high-level declarative API.

---

## BeanPostProcessor

low-level Spring container extension point.

---

# Итог

|Aspect|BeanPostProcessor|
|---|---|
|method interception|bean lifecycle|
|проще|мощнее|
|declarative|infrastructure-level|
|бизнес cross-cutting logic|framework logic|
|runtime calls|bean creation phase|


----
#### [[BeanPostProcessor proxifying vs Spring Aspects - Spring AOP - Flashcards|Link to flashcards]]



---
### References:

