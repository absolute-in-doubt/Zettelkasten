
31-05-2026 10:24

Status:

Tags: [[Resilience4j]] [[System Design]]

---
# Retry mechanism implementation - Resilience4j


Retry — это паттерн устойчивости, который **автоматически повторяет выполнение операции при ошибке**, если она считается временной (transient failure), например:

- сетевые ошибки
    
- таймауты
    
- временная недоступность сервиса

Идея: _“попробовать ещё раз, потому что ошибка может исчезнуть”_. 


---
## 2. Базовая модель работы

Retry в Resilience4j работает так:

1. Выполняется первый вызов
    
2. Если успех → всё ок
    
3. Если ошибка → проверяется политика retry
    
4. Если можно retry → ждём → повторяем
    
5. Повторяем до `maxAttempts`

Важно:

- `maxAttempts` включает **первый вызов**
    
- default: обычно **3 попытки** 


---

## 3. Основная конфигурация (RetryConfig)

### Ключевые параметры:

- **maxAttempts**
    
    - сколько всего попыток (включая первую)
        
- **waitDuration**
    
    - фиксированная пауза между попытками
        
- **intervalFunction**
    
    - кастомная логика задержки (например exponential backoff)
        
- **retryExceptions**
    
    - какие исключения считаются retryable
        
- **ignoreExceptions**
    
    - какие исключения НЕ ретраить
        
- **retryOnResultPredicate**
    
    - retry не только по exception, но и по “плохому результату” (например HTTP 500)
        
- **failAfterMaxAttempts**
    
    - бросать исключение после исчерпания попыток
        


---

## 4. Политики retry (что считается ошибкой)

Retry может срабатывать по:

##### 1) Exception

```java
retryExceptions(IOException.class, TimeoutException.class)
```

##### 2) Predicate по результату

```java
retryOnResult(response -> response.getStatus() == 500)
```

##### 3) Exception predicate

```java
retryOnException(e -> e instanceof WebServiceException)
```

---

## 5. Backoff стратегии (задержки между попытками)

### Варианты:

- фиксированная задержка
    
- exponential backoff
    
- random jitter
    
- кастомная IntervalFunction
    

Примеры:

```java
IntervalFunction.ofExponentialBackoff()
IntervalFunction.of(Duration.ofSeconds(5))
IntervalFunction.ofRandomized()
```

---

## 6. Как используется в коде (декораторы)

Resilience4j работает через **decorators (обёртки)**:

```java
Retry retry = Retry.ofDefaults("service");

Supplier<String> decorated = Retry.decorateSupplier(
    retry,
    service::call
);
```

Можно оборачивать:

- Supplier
    
- Runnable
    
- Callable
    
- CompletionStage (async)
    

---

## 7. Spring Boot интеграция

Обычно есть 2 подхода:

##### 1) Аннотация

```java
@Retry(name = "userService", fallbackMethod = "fallback")
```

##### 2) Programmatic (через Retry.decorate…)

— более гибкий и тестируемый вариант


---
## 8. Retry + Circuit Breaker (очень важно)

В проде **retry почти всегда используется вместе с circuit breaker**:

- Retry → “попробовать ещё раз”
    
- CircuitBreaker → “не долбить сломанный сервис”
    

👉 без circuit breaker retry может создать “шквал запросов” при падении сервиса

---

## 9. Типовые best practices

- не делать много retry (обычно 2–3 попытки)
    
- обязательно ставить timeout + backoff
    
- использовать randomised backoff, чтобы избежать “thundering herd”
    
- комбинировать с CircuitBreaker
    
- ретраить только **transient ошибки**, не бизнес-ошибки


----
#### [[Retry mechanism implementation - Resilience4j - Flashcards|Link to flashcards]]



---
### References:

