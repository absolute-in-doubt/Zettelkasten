
08-04-2026 17:26

Status:

Tags: [[Testcontainers]]

---
# Overview - Kafka Testcontainers

### Простой интеграционный тест с KafkaContainer

Тестируем, что Kafka работает, продюсер может записать сообщение, а консьюмер прочитать его обратно.

```java
@Testcontainers
public class KafkaSmokeTest {

    // Объявляем KafkaContainer как @Container — Testcontainers сам поднимет и убьёт его    
    @Container    
    static final KafkaContainer kafka = new KafkaContainer(        
	    DockerImageName.parse("apache/kafka-native:3.8.0")    
	);    
	
	@Test    
	void produceConsume() throws Exception {        
		// Получаем bootstrap-адрес запущенного брокера        
		String bootstrap = kafka.getBootstrapServers();        
		
		// Конфигурируем Kafka-продюсер        
		Properties producerProps = new Properties();        
		producerProps.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrap);        
		producerProps.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, 
		StringSerializer.class.getName());        
		producerProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, 
		StringSerializer.class.getName());        
		
		// Создаём продюсер и отправляем одно сообщение в demo-topic        
		KafkaProducer<String, String> producer = 
new KafkaProducer<>(producerProps);   
		producer.send(
			new ProducerRecord<>("demo-topic", "my-key", "Hello Kafka!")
		).get();
		producer.close();        
		
		// Конфигурируем Kafka-консьюмер        
		Properties consumerProps = new Properties();        
		consumerProps.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrap);        
		consumerProps.put(ConsumerConfig.GROUP_ID_CONFIG, "test-group");        
		consumerProps.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");        
		consumerProps.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, 
		StringDeserializer.class.getName());        
		consumerProps.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, 
		StringDeserializer.class.getName());        
		
		// Создаём консьюмера и подписываемся на тот же топик        
		KafkaConsumer<String, String> consumer = 
new KafkaConsumer<>(consumerProps); 
		consumer.subscribe(List.of("demo-topic"));        
		
		// Ждём сообщения        
		ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(5));        
		
		// Проверяем, что пришло именно то сообщение        
		assertEquals(1, records.count());        
		assertEquals("Hello Kafka!", records.iterator().next().value());    
	}
}
```

Контейнер Kafka поднимается автоматически перед тестом, и не нужно ничего запускать вручную. `apache/kafka-native:3.8.0` это официальный Kafka‑образ, работающий в KRaft‑режиме (без ZooKeeper).


---
#### Создание топиков вручную через AdminClient

По дефолту Kafka может создавать топики при первом продюсе. Но это плохая практика, продакшене чаще всего `auto.create.topics.enable=false`. Поэтому явно создаём нужные топики через AdminClient.

```java
try (AdminClient admin = AdminClient.create(Map.of(        
AdminClientConfig.BOOTSTRAP_SERVERS_CONFIG, kafka.getBootstrapServers()
))) {    
	List<NewTopic> topics = List.of(        
		new NewTopic("input", 1, (short)1),        
		new NewTopic("primes", 1, (short)1),        
		new NewTopic("composites", 1, (short)1),        
		new NewTopic("dlq", 1, (short)1)    
	);    
	
	admin.createTopics(topics).all().get();
}
```

- `new NewTopic("имя", партиции, фактор репликации)` — в тестах у нас один брокер, так что `replicationFactor` должен быть строго `1`. 
- `createTopics(...).all().get()` блокирует выполнение до полной регистрации тем на брокере. Без этого возможны гонки.


----
#### [[Overview - Kafka Testcontainers - Flashcards|Link to flashcards]]



---
### References:

- https://habr.com/ru/companies/otus/articles/931264/