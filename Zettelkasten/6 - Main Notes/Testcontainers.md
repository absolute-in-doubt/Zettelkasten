
Index: [[Testing]] [[Java+]]


[[Overview - Testcontainers]]

```xml
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>testcontainers-bom</artifactId>
    <version>1.21.2</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>

<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>junit-jupiter</artifactId>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>postgresql</artifactId>
    <scope>test</scope>
</dependency>
```

Или со Spring Boot:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-testcontainers</artifactId>
    <scope>test</scope>
</dependency>
<dependency>  
    <groupId>org.testcontainers</groupId>  
    <artifactId>testcontainers-junit-jupiter</artifactId>  
    <scope>test</scope>  
</dependency>  
<dependency>  
    <groupId>org.testcontainers</groupId>  
    <artifactId>testcontainers-postgresql</artifactId>  
    <scope>test</scope>  
</dependency>
```


- [[@ServiceConnection - Testcontainers]] - основной способ работы с тестконтейнерами сейчас (меньше ебли с настройкой)

- [[Основные аннотации - Testcontainers]]
	- [[configuration class - Testcontainers]] - TestcontainersConfiguration class


- [[GenericContainer - Testcontainers]]


---
#### Postgres

- [[Overview - Postgres Testcontainer]]



#### Kafka

- [[Overview - Kafka Testcontainers]]

- [[KafkaContainer vs ConfluentKafkaContainer - Testcontainers]]

#### Kafka Schema Registry

- [[Kafka Schema Registry - Testcontainers]]


----
### References:

- [[Kafka]]
- [[Spring Kafka]]