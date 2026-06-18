
18-03-2026 19:24

Status:

Tags: [[Spring Kafka]] [[Kafka]]

---
# @KafkaListener принцип работы прокси- Spring Kafka

**~={purple}@KafkaListener=~** **регистрирует метод как Kafka consumer** и создаёт **MessageListenerContainer**. Работает через **AOP прокси**.


**Что делает прокси:**

- ✅ **Конвертация** `ConsumerRecord → @Payload/@Header` параметры
    
- ✅ **Error handling** (Retry, Dead Letter)
    
- ✅ **Offset commit** по `ack-mode`
    
- ✅ **Batch processing** (если `containerFactory=batch`)
    
- ✅ **Validation** (`@Valid` на payload)

#### Последовательность действий в прокси

```
1. KafkaListenerAnnotationBeanPostProcessor сканирует @KafkaListener
2. Создаётся MethodKafkaListenerEndpoint  
3. Создаётся ConcurrentMessageListenerContainer
4. Container запускает KafkaConsumer в отдельном потоке
5. AOP прокси оборачивает метод в MessagingMessageListenerAdapter
```


## Детали прокси-обработки

1. **MethodKafkaListenerEndpoint**

```
Annotation → Endpoint → Container → Consumer.poll()
         ↓
    MessagingMessageListenerAdapter (AOP Proxy)
         ↓
    invokeListenerMethod()
```

2. **MessagingMessageListenerAdapter** (главный прокси)

```java
// Псевдокод внутри прокси
public void onMessage(ConsumerRecord record) {
    Object[] args = resolveArgs(record);           // конвертация
    Object result = methodInvoker.invoke(args);    // вызов метода
    handleResult(result);                         // обработка возврата
    commitOffset();                               // коммит offset
}
```

3. **resolveArgs()** — резолвинг аргументов

```java
@KafkaListener(topics = "test")
public void listen(
    @Payload String data,           // value из record
    @Header("kafka_receivedTopic") String topic,  // header
    Acknowledgment ack              // для manual commit
) {}
```

> [!warning] 
> Если используем передачу не в PLAINTEXT, а например через Avro, то он мапится только в `GenericRecord/SpecificRecord`:
> ```java
> @KafkaListener(topics = "test")
>public void consume(@Payload GenericRecord record,
>                   @Header("kafka_receivedTopic") String topic) {
>    // ✅ topic из header работает
>}
>
> ```
> 


4. **Offset commit lifecycle**

```
poll() → process batch → commit (BATCH/MANUAL) → poll()
     ↓ exception
repeat batch (если retry) → DLT (если DeadLetterPublishingRecoverer)
```

----
#### [[@KafkaListener - Spring Kafka - Flashcards|Link to flashcards]]



---
### References:

