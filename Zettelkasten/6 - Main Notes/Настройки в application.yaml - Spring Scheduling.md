
31-05-2026 08:48

Status:

Tags: [[Spring Scheduling]] [[Spring]]

---
# Настройки в application.yaml - Spring Scheduling


В Spring Boot можно настроить часть инфраструктуры Scheduling через `application.yaml`, не создавая собственные бины.

|Настройка|Назначение|
|---|---|
|`spring.task.scheduling.pool.size`|размер пула потоков|
|`spring.task.scheduling.thread-name-prefix`|префикс имен потоков|
|`spring.task.scheduling.shutdown.await-termination`|ждать завершения задач при shutdown|
|`spring.task.scheduling.shutdown.await-termination-period`|максимальное время ожидания|
|`@Scheduled(cron = "${...}")`|вынести cron в конфиг|
|`@Scheduled(fixedRateString = "${...}")`|вынести период в конфиг|
|`@Scheduled(fixedDelayString = "${...}")`|вынести задержку в конфиг|

Для типичного Spring Boot приложения чаще всего настраивают только **размер пула (`pool.size`)**, а сами расписания (`cron`, `fixedRate`, `fixedDelay`) выносят в отдельные application properties.



---
### Размер пула потоков

По умолчанию Boot создает `ThreadPoolTaskScheduler` с небольшим пулом (исторически часто 1 поток).

Можно настроить:

```yaml
spring:
  task:
    scheduling:
      pool:
        size: 10
```

Эквивалентно:

```java
ThreadPoolTaskScheduler scheduler = new ThreadPoolTaskScheduler();
scheduler.setPoolSize(10);
```

---

### Префикс имен потоков

Полезно для логирования и отладки:

```yaml
spring:
  task:
    scheduling:
      thread-name-prefix: scheduler-
```

В логах увидите:

```text
scheduler-1
scheduler-2
scheduler-3
```

вместо:

```text
pool-1-thread-1
```

---

### Shutdown-поведение

##### Ждать завершения задач

```yaml
spring:
  task:
    scheduling:
      shutdown:
        await-termination: true
```

При остановке приложения Spring будет ждать завершения активных scheduled-задач.


---
##### Максимальное время ожидания

```yaml
spring:
  task:
    scheduling:
      shutdown:
        await-termination-period: 30s
```

Например:

```yaml
spring:
  task:
    scheduling:
      shutdown:
        await-termination: true
        await-termination-period: 1m
```

Означает:

```text
SIGTERM
↓
ждать до 1 минуты
↓
если задачи не завершились — принудительное завершение
```

Очень полезно для Kubernetes graceful shutdown.


---
# Что нельзя настроить через application.yaml

Нельзя напрямую задать:

### Cron

```java
@Scheduled(cron = ...)
```

Но можно вынести в property:

```java
@Scheduled(cron = "${jobs.cleanup.cron}")
```

```yaml
jobs:
  cleanup:
    cron: "0 */5 * * * *"
```

---

### TriggerTask

```java
registrar.addTriggerTask(...)
```

Только программная конфигурация.

---

### Собственный TaskScheduler

Если нужен:

```java
scheduler.setErrorHandler(...)
scheduler.setClock(...)
scheduler.setTaskDecorator(...)
```

или какая-то нестандартная реализация, придется объявлять бин вручную.

---

## Что ещё часто выносят в properties

### Fixed rate

```java
@Scheduled(fixedRateString = "${jobs.sync.rate}")
```

```yaml
jobs:
  sync:
    rate: 5000
```

---

### Fixed delay

```java
@Scheduled(fixedDelayString = "${jobs.sync.delay}")
```

```yaml
jobs:
  sync:
    delay: 10s
```

В современных версиях Spring поддерживаются `Duration`-форматы:

```yaml
jobs:
  sync:
    delay: 30s
```

```yaml
jobs:
  sync:
    delay: 5m
```

```yaml
jobs:
  sync:
    delay: 1h
```

---

## Часто используемый набор настроек

```yaml
spring:
  task:
    scheduling:
      pool:
        size: 8
      thread-name-prefix: scheduler-
      shutdown:
        await-termination: true
        await-termination-period: 30s
```

Для большинства production-приложений этого достаточно.


----
#### [[Настройки в application.yaml - Spring Scheduling - Flashcards|Link to flashcards]]



---
### References:

