
31-05-2026 10:31

Status:

Tags: [[Resilience4j]] [[System Design]] [[Java+]]

---
# Time Limiter - Resilience4j



**TimeLimiter** — это модуль Resilience4j, который **ограничивает максимальное время выполнения асинхронной операции**.

👉 Если операция не успела завершиться за заданное время → выбрасывается `TimeoutException` и выполнение прерывается.

📌 Это защита от:

- зависших HTTP-запросов
    
- медленных downstream сервисов
    
- “вечных” future/async операций
    


---

## 3. Ключевая идея работы

TimeLimiter работает **только с асинхронными типами**:

- `CompletionStage`
    
- `CompletableFuture`
    

❗ В Spring это важно:

> синхронные методы напрямую не поддерживаются

---

## 4. Как он работает внутри

1. Вы запускаете async operation (CompletableFuture)
    
2. TimeLimiter запускает таймер
    
3. Если операция завершилась → OK
    
4. Если нет → после timeout:
    
    - бросается `TimeoutException`
        
    - future отменяется (если включено `cancelRunningFuture`)
        

---

## 5. Основная конфигурация

### TimeLimiterConfig

```java
TimeLimiterConfig config = TimeLimiterConfig.custom()
    .timeoutDuration(Duration.ofSeconds(2))
    .cancelRunningFuture(true)
    .build();
```

### Параметры:

- **timeoutDuration**
    
    - максимальное время выполнения
        
- **cancelRunningFuture**
    
    - отменять ли реально выполняющуюся задачу
        

---

## 6. Использование (decorator)

```java
TimeLimiter timeLimiter = TimeLimiter.of(config);

Supplier<CompletionStage<String>> decorated =
    TimeLimiter.decorateCompletionStage(
        timeLimiter,
        scheduledExecutor,
        () -> service.callAsync()
    );
```

---

## 7. Spring Boot аннотация

```java
@TimeLimiter(name = "userService", fallbackMethod = "fallback")
```

⚠️ Требование:

```java
CompletableFuture<T> method()
```

❗ Если метод возвращает `T`, будет ошибка:

> IllegalReturnTypeException

---

## 8. Fallback

Можно указать fallback при таймауте:

```java
public CompletableFuture<String> fallback(Throwable ex) {
    return CompletableFuture.completedFuture("default");
}
```

---

## 9. Важные особенности

### ⚠️ 1. Только async

TimeLimiter **не работает с обычными методами** (`String`, `User`, etc.)

---

### ⚠️ 2. Использует scheduler

Внутри используется `ScheduledExecutorService` для отслеживания таймаута.

---

### ⚠️ 3. TimeLimiter ≠ Thread timeout

Он не “убивает поток” напрямую — он:

- отменяет Future
    
- логически прерывает выполнение
    

---

## 10. Spring Cloud behavior

В Spring Cloud CircuitBreaker:

- TimeLimiter включён по умолчанию
    
- можно отключить глобально:
    

```yaml
spring.cloud.circuitbreaker.resilience4j.disable-time-limiter: true
```

---

## 11. Где используется в реальности

TimeLimiter обычно ставят:

- HTTP client calls (Feign/WebClient)
    
- gRPC calls
    
- calls к external APIs
    
- async orchestration flows
    

---

## 12. Best practices

- ставить timeout **всегда меньше retry timeout**
    
- комбинировать с:
    
    - CircuitBreaker
        
    - Retry
        
- использовать jitter + backoff (если retry включён)
    
- не делать слишком маленький timeout (ложные фейлы)
    

---

## 13. Ментальная модель

```
async call → start timer → success OR timeout → fallback
```

---

## 14. Частая ошибка

❌ “обернул синхронный метод @TimeLimiter”  
→ не работает

✔ правильно:

- перевести в `CompletableFuture`
    
- или использовать WebFlux timeout / gateway timeout
    

---

Если хочешь, дальше могу сделать:

- 🔥 как правильно комбинировать **Retry + TimeLimiter + CircuitBreaker (production pattern)**
    
- 🔥 или почему TimeLimiter часто “бесполезен” без правильного thread model в Spring Boot


----
#### [[Time Limiter - Resilience4j - Flashcards|Link to flashcards]]



---
### References:

