
18-03-2026 19:20

Status:

Tags: [[Spring Kafka]] [[Kafka]]

---
# Коммит оффсетов - Spring Kafka


```
1. Container вызывает consumer.poll()
2. Listener обрабатывает batch сообщений
3. Spring коммитит offset **ПОСЛЕ** обработки batch [web:124]
```

#### Настройки AckMode:

```
spring:
  kafka:
    listener:
      ack-mode: batch     # default: коммит после batch
      # ack-mode: record   # коммит после каждого сообщения
      # ack-mode: manual   # ручной коммит через Acknowledgment
```



#### Использование **~={purple}@Transactional=~** на `consume()` методе


```java
@Transactional
@KafkaListener(topics = "payments")
public void consume(PaymentEvent event) {
    // БД операция
    paymentRepository.save(event);
    // Если exception → rollback БД + offset НЕ коммитится
}
```

```
1. poll() → messages
2. listener() → exception 
3. Spring НЕ коммитит offset (batch откатывается)
4. Restart → читает с начала batch
```


---
### Manual + ~={purple}@Transactional=~

```java
@KafkaListener(topics = "payments")
public void process(PaymentEvent event, Acknowledgment ack) {
    try {
        paymentRepository.save(event);
    } catch (Exception e) {
        log.error("Failed", e);
        return; // НЕ коммитим
    }
    ack.acknowledge(); // Только при успехе
}
```


----
#### [[Коммит оффсетов - Spring Kafka - Flashcards|Link to flashcards]]



---
### References:
