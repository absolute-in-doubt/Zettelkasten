
31-05-2026 08:38

Status:

Tags: [[Spring Scheduling]] [[Spring]]

---
# Использование @Scheduling с другими аннотациями - Spring Scheduling


### ~={purple}@Scheduled=~ ~={red}НЕ использует Spring AOP-прокси=~


Spring через `ScheduledAnnotationBeanPostProcessor` находит метод рефлексией и регистрирует его в планировщике. Прокси для самого факта запуска не требуется. Поэтому даже такое работает:

```java
@Scheduled(fixedRate = 1000)
private void execute() {
}
```

Хотя `private` метод для обычного Spring AOP проксироваться не может.


---
## Ограничения и особенности проксирования



### Self-invocation не ломает @Scheduled

Для `@Transactional` проблема:

```java
this.someTransactionalMethod();
```

Прокси обходится.

Для самого `@Scheduled` такой проблемы нет, потому что запуск делает планировщик, а не прокси. 

---

## Можно ли использовать @Transactional?

Да.

Очень частый сценарий:

```java
@Scheduled(cron = "0 * * * * *")
@Transactional
public void processOrders() {
}
```

При вызове задачи планировщик получает бин из контекста. Если бин завернут в транзакционный прокси, вызов пройдет через него и транзакция откроется нормально.

На практике комбинация:

```java
@Scheduled + @Transactional
```

используется постоянно.

---

### Но есть нюанс

Если задача работает долго:

```java
@Scheduled(...)
@Transactional
public void sync() {
    // запросы в БД
    // HTTP вызовы
    // работа с файлами
}
```

транзакция будет открыта всё время выполнения задачи.

Это может:

- удерживать connection из пула;
    
- держать блокировки;
    
- снижать пропускную способность системы. 
    

Поэтому обычно рекомендуют:

```java
@Scheduled
public void job() {
    service.processChunk();
}
```

а уже внутри сервиса:

```java
@Transactional
public void processChunk() {
}
```

Держать транзакции как можно короче.

---

## Можно ли использовать Resilience4j?

Да.

Например:

```java
@Scheduled(fixedDelay = 5000)
@Retry(name = "externalApi")
public void poll() {
}
```

или

```java
@Scheduled
@CircuitBreaker(name = "externalApi")
public void poll() {
}
```

Обычно работает без проблем.

Причина та же: когда планировщик вызывает метод, он вызывает уже итоговый Spring-бин, который может быть обернут:

```text
Scheduler
    ↓
Resilience4j Proxy
    ↓
Transactional Proxy
    ↓
Target Bean
```

Поэтому комбинации вроде:

```java
@Scheduled
@Retry
@Transactional
```

вполне нормальны.

---

## Можно ли использовать вместе с @Async?

Да.

```java
@Scheduled(fixedRate = 5000)
@Async
public void execute() {
}
```

Тогда scheduler лишь инициирует запуск, а сама работа уйдет в асинхронный executor.

Полезно для длительных задач.


#### ~={purple}@Scheduling=~ + ~={purple}@Async=~ - используется для разделения ответственности


```
TaskScheduler
    отвечает только за расписание

TaskExecutor
    отвечает за выполнение работы
```

Например:

```java
TaskScheduler  -> pool 2
TaskExecutor   -> pool 50
```


Есть задача:

```java
@Scheduled
@Async("businessExecutor")
public void processSmth(){}
```



----
#### [[Использование @Scheduling с другими аннотациями - Spring Scheduling - Flashcards|Link to flashcards]]



---
### References:

