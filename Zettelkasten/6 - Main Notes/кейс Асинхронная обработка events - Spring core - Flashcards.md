
Theory for the cards: [[кейс Асинхронная обработка events - Spring core]]

FILE TAGS: spring spring_core system_design

Q: Как в этом случае можно вынести логику отправки сообщений серверу статистики, чтобы не дожидаться пока это выполнится транзакционно, и если сообщение не отправилось - похуй, не откатывать транзакцию?
```java
@Component 
@RequiredArgsConstructor 
public class Restaurant { 
	private final ApplicationEventPublisher eventPublisher; 
	private final StatisticsService statisticsSerrvice; 
	
	@Transactional 
	public void placeOrder(String orderDetails){ 
		// writes to the DB 
		orderService.save(orderDetails); 
		statisticsService.sendLogs(orderDetails);
	} 
}
```
A: В теории для выноса отправки логов из транзакции достаточно вынести в отдельный метод:
```java
@Component 
@RequiredArgsConstructor 
public class Restaurant { 
	private final ApplicationEventPublisher eventPublisher; 
	private final StatisticsService statisticsSerrvice; 
	
	public voidPlaceOrder(String orderDetails){
		statisticsService.sendLogs(orderDetails);
		placeOrderTransactional(orderDetails);
	}
	
	@Transactional 
	public void placeOrderTransactional(String orderDetails){ 
		// writes to the DB 
		orderService.save(orderDetails); 
	} 
}
```
	
**НО**
- указанная выше реализация не будет работать, т.к. вызываем метод у самого сервиса, а не через прокси -> нужен self-inject -> грязно
`+` это не асинхронно, пользователю все равно придётся ждать отправку логов
	
---
**Решение:** Spring events + асинхронность
	
```java
@Component 
@RequiredArgsConstructor 
public class Restaurant { 
	private final ApplicationEventPublisher eventPublisher; 
	private final OrderService orderSerrvice; 
	
	@Transactional 
	public void placeOrder(String orderDetails){ 
		// writes to the DB 
		orderService.save(orderDetails); 
		
		eventPublisher.publishEvent( 
			new OrderCreatedEvent(this.orderDetails) 
		); 
	} 
}
```
	
**НО** 
Для **асинхронности** нужны **явные настройки**:
	
- `@EnableAsync` в конфигурации приложения.
    
- `@Async` **на методе-слушателе** `@EventListener`:
    
```java
@Component
public class OrderEventListener {
    @Async  
    @EventListener
    public void handle(OrderCreatedEvent event) {
        // выполняется в пуле потоков (TaskExecutor)
    }
}
```
	
Тогда `publishEvent()` вернётся **немедленно**, а обработка уйдёт в фон — **метод `placeOrder()` не ждёт**.
<!--ID: 1776268352948-->
