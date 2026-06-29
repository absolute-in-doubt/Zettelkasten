
Theory for the cards: [[@Lazy - Spring Core]]

FILE TAGS: spring spring_core

Q: Расскажи про аннотацию `@Lazy`: для чего она нужна, куда ставится и как работает под капотом.
A: **@Lazy** включает `lazy initialization` бина.
	
Без **@Lazy** Singleton bean создается `при старте ApplicationContext`.
	
С **@Lazy** Bean создается `при первом использовании`.
	
---
**Что происходит под капотом**
	
Spring inject'ит `proxy` вместо настоящего объекта.
	
**При первом вызове метода,** proxy:
	
- создает настоящий bean
    
- сохраняет его
    
- делегирует вызовы
    
---
**Часто используется для**
	
**Circular dependencies**
	
```java
A -> B
B -> A
```
	
---
**Heavy beans**
	
- expensive initialization
    
- external connections
    
- large caches
	
---
**Куда ставится**
	
**1. На bean class**
	
```java
@Service
@Lazy
public class UserService {
}
```
	
---
**2. На `@Bean`**
	
```java
@Bean
@Lazy
public UserService userService() {
    return new UserService();
}
```
	
---
**3. На dependency injection point**
	
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
	
<!--ID: 1782055202074-->

