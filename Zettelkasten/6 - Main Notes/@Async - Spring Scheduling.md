
13-09-2026 11:04

Status:

Tags: [[Spring Scheduling]] [[Spring]]

---
# @Async - Spring Scheduling

`@Async` позволяет **выполнить метод асинхронно в отдельном потоке**, не блокируя вызывающий поток.

```java
import org.springframework.scheduling.annotation.Async;
```

Зависимость — **Spring Context**:

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
</dependency>
```

В Spring Boot обычно отдельно добавлять её не нужно — `spring-context` приходит транзитивно, например через `spring-boot-starter`.


---
### Включение

Нужно активировать async processing:

```java
@Configuration
@EnableAsync
public class AsyncConfig {
}
```

После этого:

```java
@Service
public class NotificationService {

    @Async
    public void sendNotification() {
        // выполняется асинхронно
    }
}
```

Вызов:

```java
notificationService.sendNotification();
```

вернётся **сразу**, а `sendNotification()` продолжит выполняться в другом потоке.



---
### Executor

> [!warning]
> По умолчанию Spring использует `SimpleAsyncTaskExecutor`, который:
> - создаёт новый поток под каждую задачу
> - НЕ ПЕРЕИСПОЛЬЗУЕТ потоки
> - может вызвать thread saturation - все потоки в пуле потоков заняты а новые запросы вынуждены ждать в очереди.


По умолчанию Spring использует executor, который зависит от конфигурации контекста. Для production обычно лучше определить собственный:

```java
@Configuration
@EnableAsync
public class AsyncConfig {
	
	@Bean(name="taskExecutor")
	public Executor taskExecutor() {
	    ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
	
	    executor.setCorePoolSize(5);
	    executor.setMaxPoolSize(10);
	    executor.setQueueCapacity(100);
	    executor.setThreadNamePrefix("async-");
	
	    executor.initialize();
	    return executor;
	}
}
```

И указать его:

```java
@Async("taskExecutor")
public void sendNotification() {
}
```

---

### Возвращаемое значение

Можно использовать `Future`, `CompletableFuture`:

```java
@Async
public CompletableFuture<String> process() {
    return CompletableFuture.completedFuture("result");
}
```

Вызывающий код получает `CompletableFuture`, а не сам результат.

---

### ⚠️ Важные ограничения

**1. Работает через Spring Proxy**

Такой вызов **не будет асинхронным**:

```java
this.sendNotification();
```

Если один метод класса вызывает другой `@Async`-метод через `this`, proxy обходится.

Лучше вызывать через другой Spring Bean:

```text
Controller
   ↓
Service A
   ↓
Service B (@Async)
   ↓
Executor → отдельный thread
```

**2. Исключения**

Для `void`-метода исключение нельзя получить через вызывающий код напрямую. Для обработки можно настроить `AsyncUncaughtExceptionHandler`.

Для `CompletableFuture` исключение можно обработать через:

```java
future.exceptionally(...)
```

**3. `@Transactional`**

`@Async` запускает выполнение в **другом потоке**, поэтому transaction context исходного потока туда автоматически не переносится.

Это особенно важно при комбинации:

```java
@Transactional
@Async
```

Нельзя рассчитывать, что асинхронный метод продолжит исходную транзакцию.

---

### 🧠 Что сказать на собеседовании

> `@Async` — это Spring-аннотация для выполнения метода асинхронно через `TaskExecutor`. Она работает через Spring AOP proxy, поэтому self-invocation не сработает. Обычно для production настраивают собственный `ThreadPoolTaskExecutor`. При использовании другого потока не переносится transaction context и другие ThreadLocal-контексты автоматически.


----
#### [[@Async - Spring Scheduling - Flashcards|Link to flashcards]]



---
### References:

