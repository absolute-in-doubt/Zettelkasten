
02-02-2026 08:39

Status:

Tags: [[Patterns]] [[System Design]]

---
# InBox, OutBox implementation - Patterns

**Outbox Pattern** решает проблему **Dual Writes** (двойная запись), когда нужно атомарно сохранить данные в БД **и** отправить событие в Kafka. Без Outbox'а возможна ситуация: заказ создался в БД, но событие потерялось.

Причина:
1. Создать заказ - отдельное действие 
2.  Отправить событие - отдельно действие

> [!tip] **Почему просто не отправляем сообщение в Kafka в составе транзакции?**
> Потому что, если нужно выполнить сложную логику, перед тем, как отправить событие в БД и фейлить транзакцию только из-за того, что сообщение не дошло до Kafka -> потери производительности. + при записи в БД нужны pessimistic/optimistic locks, а если optimistic lock не сможет завершить транзакцию (нужен откат) -> сообщение уже останется в Kafka.
> 
> С outbox:
> 1. Тяжелая логика - без транзакции
> 2. Сохранение в БД - с транзакцией
> 3. Делегируем загрузку событий в Kafka другому потоку -> события отправляются асинхронно

### Ещё пример

![[Pasted image 20260415182613.png]]

Если здесь в `statisticsService.newEmployee()` будет выкинута ошибка, то откатить отправку сообщения мы не можем -> используем **Transactional OutBox:**

В методе `newEmployee(Employee employee)`:
- Сохраняем в `employeeRepository`
-  **~={orange}Атомарно сохраняем в outbox таблицу=~**
- выполняем  `statisticsService.newEmployee()` 

Таким образом, если  `statisticsService.newEmployee()` выкинет ошибку - просто откатим транзакцию записи в outbox таблицу.


---
## Проблема Dual Writes

```
❌ Без Outbox:
1. INSERT INTO orders (id=123, status='created') ✅
2. producer.send("order-created") ❌ Kafka недоступен

Результат: Заказ в БД есть, событие потеряно → PaymentService не узнает!
```

## ✅ Решение: Transactional Outbox

## Шаг 1: Создание Outbox таблицы

```sql
CREATE TABLE outbox_events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    aggregate_id UUID NOT NULL,      -- order_id для ordering
    topic VARCHAR(255) NOT NULL,
    event_type VARCHAR(100) NOT NULL,
    payload JSONB NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    processed BOOLEAN DEFAULT false
);
CREATE INDEX idx_outbox_not_processed ON outbox_events(processed) WHERE NOT processed;
CREATE INDEX idx_outbox_aggregate ON outbox_events(aggregate_id);
```

## Шаг 2: Бизнес-операция + событие в одной транзакции

```java
@Transactional 
public Order createOrder(CreateOrderRequest request) {
     // 1. Сохраняем бизнес-данные    
     Order order = orderRepository.save(new Order(request.getId(), "CREATED"));         
     // 2. Создаём событие в той же транзакции!    
     OutboxEvent event = OutboxEvent.builder()        
	     .aggregateId(order.getId())        
	     .topic("orders")        
	     .eventType("OrderCreated")        
	     .payload(toJson(order))        
	     .build();    
	 outboxEventRepository.save(event);         
	 return order;  // ✅ Атомарно: order + событие или ничего! 
}
```

## Шаг 3: Relay (отправитель) — отдельный consumer


```java
@Component 
class OutboxRelay {          
	@Scheduled(fixedDelay = 1000)  // Каждую секунду    
	public void relayEvents() {        
	
		// 4. Берем необработанные события (SKIP LOCKED = конкурентность)       
		List<OutboxEvent> events = outboxRepository            .findTop100ByProcessedFalseOrderByCreatedAt(LockModeType.PESSIMISTIC_WRITE);           for (OutboxEvent event : events) {
			try {                
			
				// 5. Отправляем в Kafka                
				producer.send(
						event.getTopic(), 
						event.getAggregateId(), 
						event.getPayload()
					);                                 
					
					// 6. Помечаем как отправленное              
					event.setProcessed(true);                
					outboxRepository.save(event);                             
			} catch (Exception e) {                
				log.error("Failed to send event: {}", event.getId());                
				// Retry в следующей итерации            
			}        
		}    
	} 
}
```


## Шаг 4: Consumer в другом сервисе (Inbox)


```java
// PaymentService получает событие 
if (inboxRepository.existsByEventId(eventId)) {
     return;  // Дубликат — игнорируем 
 } 
 processPayment(orderId); 
 inboxRepository.markProcessed(eventId);
```

## Полная последовательность


```
1. Клиент: POST /orders → OrderService.createOrder() 
   
2. ✅ ACID Transaction:    
   INSERT orders (id=123, status='created')   
   INSERT outbox_events (id=uuid, aggregate_id=123, topic='orders', ...) 

3. Relay: 
   SELECT * FROM outbox WHERE processed=false FOR UPDATE SKIP LOCKED 
   
4. Relay → Kafka producer.send("orders", key=123, payload=...) 
   
5. Kafka доставляет → PaymentService.inbox(event) 
   
6. PaymentService: processPayment(123) Если шаг 2 провалился → ничего не создано Если Kafka недоступен → событие останется в outbox до доставки
```

## Преимущества

|Без Outbox|С Outbox|
|---|---|
|❌ Dual writes|✅ ACID БД+событие|
|❌ Потеря событий|✅ Eventually consistent|
|❌ Сложный rollback|✅ Автоматический retry|
|❌ Race conditions|✅ Pessimistic locks|

## Очистка (важно!)


```sql
-- Удаляем старые события (раз в сутки) 
DELETE FROM outbox_events  WHERE processed=true AND created_at < NOW() - INTERVAL '7 days';
```

## Продвинутые варианты

> [!warning] 
> Чтобы избежать конкурентности при отправке событий из Outbox таблицы используем `SKIP LOCKED`.

```
✅ Debezium CDC: читает outbox автоматически (zero polling) 

✅ Kafka Connect: JDBC Source Connector + Outbox SMT 

✅ Partition key = aggregate_id: сохраняет порядок событий
```

**Вывод**: **Outbox = 100% гарантия "если данные в БД → событие в Kafka"**. Dual Writes решена через единую ACID-транзакцию + асинхронную доставку.

----
#### [[InBox, OutBox implementation - Patterns - Flashcards|Link to flashcards]]



---
### References:

- [[InBox, OutBox patterns - Patterns]]