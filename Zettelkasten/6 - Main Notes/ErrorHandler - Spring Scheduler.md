
31-05-2026 08:52

Status:

Tags: [[Spring Scheduling]] [[Spring]]

---
# ErrorHandler - Spring Scheduler


`ErrorHandler` — это обработчик необработанных исключений, возникающих при выполнении `@Scheduled` задач.

Пакет:

```java
org.springframework.util.ErrorHandler
```

Интерфейс очень простой:

```java
@FunctionalInterface
public interface ErrorHandler {
    void handleError(Throwable t);
}
```

---

## Зачем нужен

Если scheduled-задача выбрасывает исключение:

```java
@Scheduled(fixedRate = 5000)
public void sync() {
    throw new RuntimeException("Boom");
}
```

Spring перехватывает его внутри scheduler-а.

Без собственного `ErrorHandler` исключение обычно просто логируется.

---

## Настройка

Через собственный `ThreadPoolTaskScheduler`:

```java
@Bean
public TaskScheduler taskScheduler() {
    ThreadPoolTaskScheduler scheduler =
            new ThreadPoolTaskScheduler();

    scheduler.setPoolSize(10);

    scheduler.setErrorHandler(
            ex -> log.error("Scheduled task failed", ex)
    );

    return scheduler;
}
```

---

## Что можно делать в ErrorHandler

### Логирование

Самый частый вариант:

```java
scheduler.setErrorHandler(
        ex -> log.error("Task failed", ex)
);
```

---

### Метрики

```java
scheduler.setErrorHandler(ex -> {
    metrics.counter("scheduled.errors").increment();
});
```

---

### Уведомления

```java
scheduler.setErrorHandler(ex -> {
    telegramNotifier.send(ex.getMessage());
});
```

---

## Важное ограничение

`ErrorHandler` **не предотвращает исключение и не делает retry**.

Он вызывается уже после того, как ошибка произошла.

Для повторных попыток используют:

- `@Retry` из Resilience4j;
    
- `RetryTemplate`;
    
- собственную retry-логику.
    

---

## Взаимодействие с @Transactional

```java
@Scheduled
@Transactional
public void process() {
    throw new RuntimeException();
}
```

Последовательность:

```text
Исключение
    ↓
Rollback транзакции
    ↓
ErrorHandler.handleError(...)
```

То есть `ErrorHandler` не влияет на транзакцию.

---

## Когда ErrorHandler не нужен

Если задача уже обрабатывает исключения самостоятельно:

```java
@Scheduled
public void process() {
    try {
        ...
    } catch (Exception ex) {
        ...
    }
}
```

то до `ErrorHandler` исключение не дойдет.

---

## Мини-конспект

|Что|Описание|
|---|---|
|Интерфейс|`org.springframework.util.ErrorHandler`|
|Метод|`handleError(Throwable)`|
|Назначение|централизованная обработка ошибок scheduled-задач|
|Где настраивается|`ThreadPoolTaskScheduler#setErrorHandler(...)`|
|Может делать retry|❌ Нет|
|Может логировать/метрики/алерты|✅ Да|
|Вызывается после rollback транзакции|✅ Да|
|Срабатывает только для необработанных исключений|✅ Да|

Типичный production-вариант:

```java
scheduler.setErrorHandler(
        ex -> log.error("Scheduled task execution failed", ex)
);
```

чтобы все ошибки фоновых задач попадали в единое место логирования и мониторинга.


----
#### [[ErrorHandler - Spring Scheduler - Flashcards|Link to flashcards]]



---
### References:

