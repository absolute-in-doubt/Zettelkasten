
14-02-2026 13:30

Status:

Tags: [[Spring]]

---
# Создание и отправка custom Events - Spring

В Spring можно создавать свои Events. Для этого достаточно расширить `ApplicationEvent`:

```java
@Component
public class OrderCreatedEvent extends ApplicationEvent {

	private final String orderDetails;
	
	public OrderCreatedEvent(Object source, String orderDetails){
		super(source);
		this.orderDetails = orderDetails;
	}
	
	public String getObrderDetails(){
		return this.orderDetails;
	}
}
```


Для прослушивания этого Event создаём соответствующий [[ApplicationListener - Spring|ApplicationListener]] или метод, принимающий наш Event + **~={purple}@EventLIstener=~**:

```java
@Component
public class OrderCreatedEventListener {
	
	//concurrent Queue
	private final BlockingQueue<String> orderQueue;
	
	public OrderCreatedEventListener(BlockingQueue<String> orderQueue){
		this.orderQueue = orderQueue;
	}
	
	@EventListener(OrderCreatedEvent.class)
	public listen(OrderCreatedEvent event){
		//pass it to the worker thread to process
		orderQueue.push(event.getOrderDetails());
	}
}
```


Отправка ивентов осуществляется с помощью `ApplicationEventPublisher`:

```java
@Component
public class Restaurant {
	private final ApplicationEventPublisher eventPublisher;
	
	//constructor
	
	public void placeOrder(String orderDetails){
		eventPublisher.publishEvent(
			new OrderCreaatedEvent(this,orderDetails)
		);
	}
}
```


----
#### [[Создание и отправка custom Events - Spring - Flashcards|Link to flashcards]]



---
### References:

