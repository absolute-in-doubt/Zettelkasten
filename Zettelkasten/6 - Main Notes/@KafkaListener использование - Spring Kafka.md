
03-02-2026 17:12

Status:

Tags: [[Spring Kafka]] [[Kafka]] [[Spring]]

---
# @KafkaListener использование - Spring Kafka


Вся настройка происходит через `Map` аргументов, передавайемых в `DefaultKafkaConsumerFactory`, на основе которой создаётся `KafkaTemplate`:


```java
@Configuration
public class KafkaConsumerConfig {

	@Value("${spring.kafka.bootstrap-servers}")
	private String bootstrapServers;
	
	
	public Map<String, Object> consumerConfig() {
		Map<String, Object> props = new HashMap<>();
		props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
		props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
		props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
		return props;
	}
	
	@Bean
	public ConsumerFactory<String String> consumerFactory() {
		return new DefaultKafkaConsumerFactory<>(consumerConfig());
	}
	
	@Bean
	public KafkaListenerContainerFactory<
	ConcurrentMessageListenerContainer<String, String> factory(
		ConsumerFactory<String String> consumerFactory
	) {
		ConcurrentKafkaListenerContainerFactory<String, String> factory = new ConcurrentKafkaListenerContainerFactory<>();
		factory.setConsumerFactory(consumerFactory);
		return factory;
	}
}
```


`KafkaListenerContainerFactory` - получает сообщения из всех топиков, всех partitions в одном потоке.


```java
@Component
public class KafkaListeners {
	
	@KafkaListener(
		topics = "mytopic",
		groupId = "mytopic_consumers"
	)
	void listener(String data){
		System.out.println("Listener received: " + data);
	}
}
```


```java
public class Sender {

    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
        context.getBean(Sender.class).send("test", 42);
    }

    private final KafkaTemplate<Integer, String> template;

    public Sender(KafkaTemplate<Integer, String> template) {
        this.template = template;
    }

    public void send(String toSend, int key) {
        this.template.send("topic1", key, toSend);
    }

}

public class Listener {

    @KafkaListener(id = "listen1", topics = "topic1")
    public void listen1(String in) {
        System.out.println(in);
    }

}

@Configuration
@EnableKafka
public class Config {

    @Bean
    ConcurrentKafkaListenerContainerFactory<Integer, String>
                        kafkaListenerContainerFactory(ConsumerFactory<Integer, String> consumerFactory) {
                        
        ConcurrentKafkaListenerContainerFactory<Integer, String> factory =
                                new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory);
        return factory;
    }

    @Bean
    public ConsumerFactory<Integer, String> consumerFactory() {
        return new DefaultKafkaConsumerFactory<>(consumerProps());
    }

    private Map<String, Object> consumerProps() {
        Map<String, Object> props = new HashMap<>();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        props.put(ConsumerConfig.GROUP_ID_CONFIG, "group");
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, IntegerDeserializer.class);
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
        // ...
        return props;
    }

    @Bean
    public Sender sender(KafkaTemplate<Integer, String> template) {
        return new Sender(template);
    }

    @Bean
    public Listener listener() {
        return new Listener();
    }

    @Bean
    public ProducerFactory<Integer, String> producerFactory() {
        return new DefaultKafkaProducerFactory<>(senderProps());
    }

    private Map<String, Object> senderProps() {
        Map<String, Object> props = new HashMap<>();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        props.put(ProducerConfig.LINGER_MS_CONFIG, 10);
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, IntegerSerializer.class);
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        //...
        return props;
    }

    @Bean
    public KafkaTemplate<Integer, String> kafkaTemplate(ProducerFactory<Integer, String> producerFactory) {
        return new KafkaTemplate<>(producerFactory);
    }

}
```




----
#### [[KafkaListener - Spring Kafka - Flashcards|Link to flashcards]]



---
### References:

