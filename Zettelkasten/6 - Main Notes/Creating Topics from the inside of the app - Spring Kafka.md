
03-02-2026 17:54

Status:

Tags: [[Spring Kafka]] [[Kafka]] [[Spring]]

---
# Creating Topics from the inside of the app - Spring Kafka

Для создания нового топика необходимо наличие `KafkaAdmin` as a Bean.

> [!note] 
> **~={orange}Spring Boot registers KafkaAdmin automaticaly=~**

> [!warning] 
> При таком создании топиков, они создаются в процессе инициализации контекста. Они создаются ПОСЛЕ `@PostConstruct` методов, но ДО окончания загрузки контекста.
>
>Логику отправки сообщения на старнте приложения логичнее всего вынести в [[ApplicationListener - Spring|ApplicationListener]]`<ContextRefreshedEvent>`


```java
@Bean
public KafkaAdmin admin() {
    Map<String, Object> configs = new HashMap<>();
    configs.put(AdminClientConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
    return new KafkaAdmin(configs);
}

@Bean
public NewTopic topic1() {
    return TopicBuilder.name("thing1")
            .partitions(10)
            .replicas(3)
            .compact()
            .build();
}

@Bean
public NewTopic topic2() {
    return TopicBuilder.name("thing2")
            .partitions(10)
            .replicas(3)
            .config(TopicConfig.COMPRESSION_TYPE_CONFIG, "zstd")
            .build();
}

@Bean
public NewTopic topic3() {
    return TopicBuilder.name("thing3")
            .assignReplicas(0, List.of(0, 1))
            .assignReplicas(1, List.of(1, 2))
            .assignReplicas(2, List.of(2, 0))
            .config(TopicConfig.COMPRESSION_TYPE_CONFIG, "zstd")
            .build();
}
```

Если не указывать partitions и replicas - default values will be applied.

> [!tip]
> By default, if the broker is not available, a message is logged, but the context continues to load. You can programmatically invoke the admin’s `initialize()` method to try again later. If you wish this condition to be considered fatal, set the admin’s `fatalIfBrokerNotAvailable` property to `true`. The context then fails to initialize.


----
#### [[Creating Topics from the inside of the app - Spring Kafka - Flashcards|Link to flashcards]]



---
### References:

