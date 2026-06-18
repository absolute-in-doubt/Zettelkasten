
01-02-2026 15:50

Status:

Tags: [[Kafka]] [[Java+]]

---
# Schema Registry (стандартизация DTO) - Kafka

**Schema Registry** предоставляет централизованный репозиторий для управления и проверки схем данных сообщений топиков, а также для сериализации и десериализации данных по сети. Производители и потребители топиков Kafka могут использовать схемы для обеспечения согласованности и совместимости данных по мере развития схем. **Schema Registry** — ключевой компонент управления данными, помогающий обеспечивать качество данных, соответствие стандартам, прозрачность происхождения данных, возможности аудита, совместную работу между командами, эффективные протоколы разработки приложений и производительность системы.

![[Pasted image 20260201155125.png]]

Schema Registry работает с:

- **Avro** (классика Kafka-мира, бинарный, компактный)
- **Protobuf** (Google Protocol Buffers)
- **JSON Schema** (читаемый, но больше размер сообщений)

> [!note]
> Реестр схем не включен в Kafka, но существует несколько его вариантов с открытым исходным кодом. Например, **[Реестр Confluent Schema](https://github.com/confluentinc/schema-registry)**.


**Schema Registry в Kafka** — **~={orange}это отдельный сервис=~**, не являющийся частью брокера Kafka. Он деплоится как standalone-приложение (REST API на порту 8081 по умолчанию), взаимодействующее с Kafka-кластером для хранения схем в специальной топике `_schemas` (compacted topic).

## Короткий обзор

- **Готовые образы**: Официальный `confluentinc/cp-schema-registry:latest` на Docker Hub; запускается с переменными вроде `-e SCHEMA_REGISTRY_KAFKASTORE_BOOTSTRAP_SERVERS=localhost:9092`.
    
- **Привязка к брокерам**: Да, обязательно — Schema Registry хранит схемы в Kafka-топике (_schemas по умолчанию), подключается через bootstrap-серверы Kafka.
    
- **Взаимодействие**: Основное с Producer/Consumer (через URL registry), но использует Kafka как backend-хранилище.​
    
- **В кластере или один**: Поддерживает кластерный режим (несколько нод для HA), с master/slave или равномерным распределением; в продакшене — кластер.​


#### Если используем protobuf


##### Protobuf + простой плагин ручной сериализации без валидации на Schema Registry

```xml
<plugin>
    <groupId>com.github.os72</groupId>
    <artifactId>protoc-jar-maven-plugin</artifactId>
    <version>3.11.4</version>
    <executions>
        <execution>
            <phase>generate-sources</phase>
            <goals>
                <goal>run</goal>
            </goals>
            <configuration>
                <protocArtifact>com.google.protobuf:protoc:3.25.3</protocArtifact>
                <inputDirectories>
                    <directory>src/main/proto</directory>
                </inputDirectories>
                <outputTargets>
                    <outputTarget>
                        <type>java</type>
                        <outputDirectory>src/main/java</outputDirectory>
                    </outputTarget>
                </outputTargets>
            </configuration>
        </execution>
    </executions>
</plugin>

<!-- Добавляем generated-sources в classpath -->
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>build-helper-maven-plugin</artifactId>
    <version>3.4.0</version>
    <executions>
        <execution>
            <phase>generate-sources</phase>
            <goals>
                <goal>add-source</goal>
            </goals>
            <configuration>
                <sources>
                    <source>src/main/java</source>
                </sources>
            </configuration>
        </execution>
    </executions>
</plugin>

```

Это plugin для генерации классов на основе `.protobuf`, в данных классах будут реализованы методы `toByteArray()`, `parseFrom()` с помощью которых будет происходить сериализация и десериализация соотетственно.

```java
byte[] bytes = myEventObj.toByteArray(); 
producer.send("orders", bytes);
```

В этом случае мы используем базовый `org.apache.kafka.common.serialization.ByteArraySerializer`

~={red}НЕТ ВАЛИДАЦИИ=~


##### Protobuf + KafkaProtobufSerializer

Используем готовое решение сериализации: `io.confluent.kafka.serializers.protobuf.KafkaProtobufSerializer`

В этом случае можно валидировать схему на Schema Registry:

```java
Map<String, Object> props = new HashMap<>();
props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, 
          KafkaProtobufSerializer.class); // Из Confluent
props.put("schema.registry.url", "http://localhost:8081");

DefaultKafkaProducerFactory<String, MyProtoMessage> factory = 
    new DefaultKafkaProducerFactory<>(props);
```

Сами классы, that mirror protobuf entities создаются с помощью protoc compiler (инструмент командной строки от Google, который преобразует файлы определений данных (`.proto`) в исходный код Java (и других языков).):

```bash
protoc --java_out=src/main/java message.proto
```


----
#### [[Schema Registry (стандартизация DTO) - Kafka - Flashcards|Link to flashcards]]



---
### References:

