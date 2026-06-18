
08-04-2026 18:49

Status:

Tags: [[Testcontainers]] [[Testing]]

---
# Kafka Schema Registry - Testcontainers

Чтобы schema registry работала вместе с нашии брокером - необходимо создать [[Network - Docker|docker network]] и подключить к ней оба контейнера.

Это можно сделать программно:

```java
@Testcontainers  
class KafkaTest {  
  
  private static final Network NETWORK = Network.newNetwork();

//Now we can start a Kafka Broker test container on this network:

	@Container  
	private static final KafkaContainer KAFKA_CONTAINER =   
	  new KafkaContainer(DockerImageName.parse("confluentinc/cp-kafka:7.5.2"))  
	    .withNetwork(NETWORK);
	    
	    
	@Container  
	private static final GenericContainer<?> SCHEMA_REGISTRY =  
	new GenericContainer<>(DockerImageName.parse("confluentinc/cp-schema-registry:7.5.2"))  
		.withNetwork(NETWORK)  
		.withExposedPorts(8081)  
		.withEnv("SCHEMA_REGISTRY_HOST_NAME", "schema-registry")  
		.withEnv("SCHEMA_REGISTRY_LISTENERS", "http://0.0.0.0:8081")  
		.withEnv("SCHEMA_REGISTRY_KAFKASTORE_BOOTSTRAP_SERVERS",  
		"PLAINTEXT://" + KAFKA_CONTAINER.getNetworkAliases().get(0) + ":9092")
		.waitingFor(Wait.forHttp("/subjects").forStatusCode(200));
```


Теперь в настройках наших producers и consumers ставим:

```java
Properties kafkaSettings = new Properties();  
kafkaSettings.put(
	StreamsConfig.BOOTSTRAP_SERVERS_CONFIG,  
	KAFKA_CONTAINER.getBootstrapServers());  
kafkaSettings.put(
	"schema.registry.url",  
	"http://" + SCHEMA_REGISTRY.getHost() + ":" + SCHEMA_REGISTRY.getFirstMappedPort());  
  
// Remaining KAFKA Settings
```
### Ну или установить [[Overview - Testcontainers#**~={purple}@DynamicPropertySource=~**|@DynamicProperties]]

----
#### [[Kafka Schema Registry - Testcontainers - Flashcards|Link to flashcards]]



---
### References:

- https://medium.com/@nuno.mt.sousa/kafka-schema-registry-junit-and-test-containers-part-i-082d0abfc804

Super clean code example:
- https://stackoverflow.com/questions/79653794/how-to-configure-schema-registry-with-testcontainers

