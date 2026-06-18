
18-05-2026 12:17

Status:

Tags: [[Spring Core]] [[Spring]]

---
# @Lazy - Spring Core




**~={purple}@Lazy=~** включает `lazy initialization` бина.

Без **~={purple}@Lazy=~** Singleton bean создается `при старте ApplicationContext`.

С **~={purple}@Lazy=~** Bean создается `при первом использовании`.


---

# Куда ставится

## 1. На bean class

```java
@Service
@Lazy
public class UserService {
}
```

---

## 2. На `@Bean`

```java
@Bean
@Lazy
public UserService userService() {
    return new UserService();
}
```

---

## 3. На dependency injection point

```java
@RequiredArgsConstructor
@Service
public class OrderService {

    @Lazy
    private final UserService userService;
}
```

> [!warning]
> Если поставить **~={purple}@Lazy=~** на injection point, то lazy будет только для этой зависимости.




---
# Что происходит под капотом

Spring inject'ит `proxy` вместо настоящего объекта.



**При первом вызове метода**

proxy:

- создает настоящий bean
    
- сохраняет его
    
- делегирует вызовы
    

---

# Часто используется для

## Circular dependencies

```java
A -> B
B -> A
```

---

## Heavy beans

- expensive initialization
    
- external connections
    
- large caches
    


---

# Если на class/bean

то lazy будет для всего bean definition.

---

# Пример circular dependency

```java
@Service
public class A {

    public A(@Lazy B b) {
    }
}
```

---

# Ограничение

`@Lazy`:

```text
не решает архитектурные проблемы
```

а только откладывает создание bean.


----
#### [[@Lazy - Spring Core - Flashcards|Link to flashcards]]



---
### References:

