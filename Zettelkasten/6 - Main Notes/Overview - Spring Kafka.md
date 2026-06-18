
03-02-2026 15:00

Status:

Tags: [[Kafka]] [[Spring Kafka]] [[Spring]]

---
# Overview - Spring Kafka

> [!example] Note
> **Нет отдельного `spring-boot-starter-kafka`!** Автоконфигурация работает через обычную зависимость `spring-kafka`, которую Spring Boot обнаруживает и подхватывает в `spring-boot-autoconfigure` модуле.

```xml
<dependency>
  <groupId>org.springframework.kafka</groupId>
  <artifactId>spring-kafka</artifactId>
  <version>4.0.2</version>
</dependency>
```

> [!warning]
> Если используем `spring-kafka` со Spring Boot - пропускаем версию -> Spring Boot сам определит подходящую версию и подтянет её:
> ```xml
><dependency>
> 	 <groupId>org.springframework.kafka</groupId>
> 	 <artifactId>spring-kafka</artifactId>
></dependency>
> ```


### Spring Kafka Test

```xml
<dependency>    
	<groupId>org.springframework.kafka</groupId>    
	<artifactId>spring-kafka-test</artifactId>
</dependency>
```


---
### Features

- `KafkaTemplate` - высокоуровневая абстракция для отправки сообщений.
    
- `KafkaMessageListenerContainer`
    
- `@KafkaListener`
    
- `KafkaTransactionManager`
    
- `Retryable Topics`
    
- `spring-kafka-test` jar with embedded kafka server


### [Spring Kafka & Spring Boot & Kafka versions compatibility](https://spring.io/projects/spring-kafka)


----
#### [[Overview - Spring Kafka - Flashcards|Link to flashcards]]



---
### References:

