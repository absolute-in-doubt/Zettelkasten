
31-05-2026 08:27

Status:

Tags: [[Spring Scheduling]] [[Spring]]

---
# Overview - Spring Scheduling



Spring Scheduling — механизм для запуска задач по расписанию внутри приложения:

- каждые N секунд (`fixedRate`)
    
- с задержкой после завершения (`fixedDelay`)
    
- по cron-выражению (`cron`)
    

Основной пакет:

```java
org.springframework.scheduling
```

| Элемент                                | Назначение                                            |
| -------------------------------------- | ----------------------------------------------------- |
| `@EnableScheduling`                    | включает scheduling                                   |
| `@Scheduled`                           | объявляет задачу                                      |
| `TaskScheduler`                        | интерфейс планировщика                                |
| `ThreadPoolTaskScheduler`              | scheduler с пулом потоков (реализация `TaskSheduler`) |
| `SchedulingConfigurer`                 | программная настройка задач                           |
| `ScheduledTaskRegistrar`               | регистрация задач                                     |
| `ScheduledAnnotationBeanPostProcessor` | ищет и регистрирует `@Scheduled`                      |



---

## ~={purple}@EnableScheduling=~


- создает `ScheduledAnnotationBeanPostProcessor`;
- создает `ScheduledTaskRegistrar`;
- находит реализации `SchedulingConfigurer`;
- вызывает их `configureTasks(...)`;
- запускает зарегистрированные задачи.

Ставится на конфигурационный класс:

```java
@Configuration
@EnableScheduling
public class SchedulingConfig {
}
```

После этого Spring начинает искать методы с `@Scheduled`. 

> [!warning]
> ~={red}**`@EnableScheduling`  нужно указывать явно, даже в Spring Boot.**=~



---
## Основные аннотации

#### ~={purple}@Scheduled=~

Главная аннотация.


##### fixedRate

Запуск через фиксированный интервал независимо от длительности выполнения.

```java
@Scheduled(fixedRate = 5000)
public void execute() {
}

@Scheduled(fixedRateString = "${application.task.scheduling.outboxRetryWorker.rateMillis}")
public void process() {
```

Если метод работает 7 секунд, а интервал 5 секунд — при наличии пула потоков возможны параллельные запуски.

---

#### fixedDelay

Следующий запуск начинается после завершения предыдущего.

```java
@Scheduled(fixedDelay = 5000)
public void execute() {
}
```

Схема:

```
работа -> 5 сек ожидания -> работа
```

---

#### cron

Запуск по cron-выражению.

```java
@Scheduled(cron = "0 0 * * * *")
public void execute() {
}
```

Каждый час.

---

#### initialDelay

Задержка перед первым запуском.

```java
@Scheduled(
    fixedRate = 10000,
    initialDelay = 5000
)
```


---
## Основные инфраструктурные бины

### TaskScheduler

Главный интерфейс планировщика.

```java
TaskScheduler
```

Отвечает за планирование выполнения задач. 

---

### ThreadPoolTaskScheduler

Самая популярная реализация. (аналог [[ThreadPoolTaskExecutor - Spring|ThreadPoolTaskExecutor]] но со scheduling)

```java
@Bean
public TaskScheduler taskScheduler() {
    ThreadPoolTaskScheduler scheduler =
            new ThreadPoolTaskScheduler();

    scheduler.setPoolSize(10);

    return scheduler;
}
```

Позволяет выполнять несколько scheduled-задач одновременно.


---
### ScheduledAnnotationBeanPostProcessor

Ключевой внутренний бин Spring. Именно он:

1. находит `@Scheduled`
    
2. регистрирует задачи
    
3. передает их в `TaskScheduler`
    

Автоматически создается через `@EnableScheduling`. 


---
### ScheduledTaskRegistrar

Используется для программной регистрации задач.

Иногда расписание нужно создавать программно:

- cron хранится в БД;
- период задается через API;
- нужно вычислять время следующего запуска динамически;
- нужно регистрировать множество однотипных задач;
- нужно использовать собственный `TaskScheduler`.

Для этого применяется `SchedulingConfigurer`:


```java
@Configuration
@EnableScheduling
public class SchedulingConfig implements SchedulingConfigurer {

    @Override
    public void configureTasks(ScheduledTaskRegistrar registrar) {

        registrar.addFixedRateTask(
            () -> System.out.println("Hello"),
            5000
        );
    }
}
```

> [!warning]
> **При регистрации тасков через `SchedulingConfigurer` `@EnableScheduling` всё равно нужен**.


---
## Что происходит по умолчанию с потоками?

Если свой `TaskScheduler` не зарегистрирован:

Spring создаёт дефолтный планировщик. Если не настроить пул потоков, можно неожиданно получить последовательное выполнение задач.

Для production почти всегда настраивают:

```java
@Bean
public ThreadPoolTaskScheduler taskScheduler() {
    ThreadPoolTaskScheduler scheduler =
            new ThreadPoolTaskScheduler();

    scheduler.setPoolSize(10);

    return scheduler;
}
```



----
#### [[Overview - Spring Scheduling - Flashcards|Link to flashcards]]



---
### References:

