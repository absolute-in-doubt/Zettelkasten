
18-03-2026 19:47

Status:

Tags: [[Spring Kafka]] [[Kafka]]

---
# @KafkaListener properties - Spring Kafka

```java
@KafkaListener(  
        autoStartup = "${spring.kafka.listener.auto-startup:true}",  
        topics={"${spring.kafka.topics.payment_charge_created}",  
                "${spring.kafka.topics.payment_charge_succeeded}"  
        },  
        clientIdPrefix = "${spring.kafka.listener.client-id-prefix}",  
        groupId = "${spring.kafka.consumer.group-id}",  
        containerFactory = "kafkaListenerContainerFactory"  
)
```


- `autoStartup` -  аннотация специально для тестов. Если `autoStartup=false` - KafkaConsumer будет создан в контексте (а если просто создадим мок - KafkaListener просто не будет загружен в контекст). Таким образом все поля будут injected и проконтролируем создание консумера, и методы можно потестить врчную:

```java
class UserConsumerTest extends BaseTest {    

	// Просто берем бин из контекста и проверяем метод consume    
	@Autowired    
	private UserConsumer userConsumer;    
	
	@Test      
	void testConsume() {        
		userConsumer.consume(buildMessage());    
	}
}
```


- `clientIdPrefix` - префикс данного консумера в логах
- `groupId` - id группы консумеров
- containerFactory - явное указание имени бина, который будет [[@KafkaListener принцип работы прокси - Spring Kafka|резолвить данную аннотацию]]

----
#### [[@KafkaListener properties - Spring Kafka - Flashcards|Link to flashcards]]



---
### References:

