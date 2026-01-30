
08-01-2026 10:07

Status:

Tags: [[Spring]]

---
# Возможности Spring AOP


AOP в Spring — это способ вынести «сквозную» логику (логирование, транзакции, безопасность, кэш) в отдельные аспекты, не размазывая её по бизнес‑методам. Аспекты навешиваются на точки выполнения (обычно вызовы методов) через прокси‑объекты.

## Основная идея AOP в Spring

- **Аспект** — класс со сквозной логикой (например, логирование вызовов сервисов).
    
- **Join point** — точка исполнения, где можно «вклиниться» (в Spring AOP это всегда вызов метода бина).
    
- **Pointcut** — выражение, определяющее, какие именно методы перехватывать (по аннотациям, пакетам, сигнатурам).
    
- **Advice** — код, который выполняется до, после или вокруг вызова метода (типы: `@Before`, `@After`, `@Around` и т.д.).
    
- **Weaving** — «вплетение» аспекта в целевой бин; в Spring происходит в рантайме через прокси (JDK динамические прокси или CGLIB).


## Простейший пример аспекта

Допустим, нужно логировать время выполнения методов, помеченных `@Logged`.

```java
// 1. Своя аннотация-маркер
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Logged {
}
```


```java
// 2. Аспект
@Aspect
@Component
public class LoggingAspect {

    @Around("@annotation(Logged)")
    public Object logExecutionTime(ProceedingJoinPoint pjp) throws Throwable {
        long start = System.currentTimeMillis();
        try {
            Object result = pjp.proceed();        // вызываем целевой метод
            return result;
        } finally {
            long duration = System.currentTimeMillis() - start;
            System.out.println(
                pjp.getSignature() + " executed in " + duration + " ms"
            );
        }
    }
}
```

```java
// 3. Применение аспекта
@Service
public class UserService {

    @Logged
    public User findUser(Long id) {
        // бизнес‑логика
    }
}
```

И обязательно включение AOP (если не Spring Boot с автоконфигурацией):

```java
@Configuration
@EnableAspectJAutoProxy
public class AopConfig {
}
```


## Как это работает

- При старте контекста Spring оборачивает подходящие бины (например, `UserService`) в прокси.

- Когда код вызывает `userService.findUser(…)`, на самом деле вызывается метод прокси.
   
- Прокси смотрит: метод подпадает под поинткат `@annotation(Logged)` — значит, запускается `logExecutionTime`.
   
- Внутри `logExecutionTime` вызывается `pjp.proceed()` — это и есть реальный вызов `findUser`. Если не вызвать `proceed()`, целевой метод не выполнится.

- После завершения целевого метода аспект логирует время и возвращает результат дальше.



---
### References:

