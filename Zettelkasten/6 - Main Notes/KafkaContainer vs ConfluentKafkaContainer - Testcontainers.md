
10-04-2026 19:53

Status:

Tags: [[Testcontainers]] [[Kafka]]

---
# KafkaContainer vs ConfluentKafkaContainer - Testcontainers

**Основные отличия между `KafkaContainer` и `ConfluentKafkaContainer` в Testcontainers для Kafka:**

## 1. **Docker-образы (базовое различие)**

- **`KafkaContainer`** использует официальные образы Apache Kafka - ~={red}**работает с любыми Kafka образами (Apache Kafka, Bitnami, Confluent)**=~:
    
```java
KafkaContainer kafka = new KafkaContainer(
    DockerImageName.parse("apache/kafka-native:3.8.0")
);
```
    
- **`ConfluentKafkaContainer`** использует образы Confluent Platform - **~={red}совместим только с confluentinc/cp-kafka:7.4.0+=~**:
    
```java
ConfluentKafkaContainer kafka = new ConfluentKafkaContainer(
    DockerImageName.parse("confluentinc/cp-kafka:7.4.0")
);
```
    

## 2. **Конфигурация и переменные окружения**

| Аспект                                   | KafkaContainer                                 | ConfluentKafkaContainer               |
| ---------------------------------------- | ---------------------------------------------- | ------------------------------------- |
| **Порты**                                | 9092 (plaintext) + 9093 (controller)           | 9092 (plaintext)                      |
| **KAFKA_ADVERTISED_LISTENERS**           | Автоматически настраивается для testcontainers | Требует явной настройки для Confluent |
| **KAFKA_LISTENER_SECURITY_PROTOCOL_MAP** | `CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT`     | Только `PLAINTEXT:PLAINTEXT`          |
| **KAFKA_INTER_BROKER_LISTENER_NAME**     | `PLAINTEXT`                                    | `PLAINTEXT`                           |

> [!warning]
> `ConfluentKafkaContainer` по дефолту запускается с Zookeeper. Чтобы запустить его в режиме KRaft - настраиваем самомтоятельно:
> ```java
> @Container
>static ConfluentKafkaContainer kafka = new ConfluentKafkaContainer(
>    DockerImageName.parse("confluentinc/cp-kafka:7.8.0"))
>    .withEnv("KAFKA_NODE_ID", "1")
>    .withEnv("KAFKA_PROCESS_ROLES", "broker,controller")
>    .withEnv("KAFKA_CONTROLLER_LISTENER_NAMES", "CONTROLLER")
>    .withEnv("KAFKA_LISTENERS", "PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093")
>    .withEnv("KAFKA_ADVERTISED_LISTENERS", "PLAINTEXT://localhost:9092")
>    .withEnv("KAFKA_LISTENER_SECURITY_PROTOCOL_MAP", "CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT")
>    .withEnv("KAFKA_INTER_BROKER_LISTENER_NAME", "PLAINTEXT")
>    .withEnv("KAFKA_CONTROLLER_QUORUM_VOTERS", "1@localhost:9093")
>    .withEnv("KAFKA_KRAFT_CLUSTER_ID", "S40B8iVtR0umuTyuM1eLjA")
>    .withEnv("CLUSTER_ID", "S40B8iVtR0umuTyuM1eLjA");
> ```


## **Когда использовать что?**

|Сценарий|Рекомендация|
|---|---|
|**Простые тесты Kafka**|`KafkaContainer` (apache/kafka-native)|
|**Confluent Platform + Schema Registry**|`ConfluentKafkaContainer`|
|**Тестирование Schema Registry**|Только `ConfluentKafkaContainer`|
|**Минимальные требования**|`KafkaContainer` (меньше overhead)|
|**Production = Confluent**|`ConfluentKafkaContainer`|


----
#### [[KafkaContainer vs ConfluentKafkaContainer - Testcontainers - Flashcards|Link to flashcards]]



---
### References:

