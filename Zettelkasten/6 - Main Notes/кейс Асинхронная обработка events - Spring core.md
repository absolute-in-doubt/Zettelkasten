
15-04-2026 18:38

Status:

Tags: [[Spring Core]] [[Spring]]

---
# кейс Асинхронная обработка events - Spring core


> [!example] **Кейс**
> ```java
>@Component 
>@RequiredArgsConstructor 
>public class Restaurant { 
>	private final ApplicationEventPublisher eventPublisher; 
>	private final OrderService orderSerrvice; 
>	
>	@Transactional 
>	public void placeOrder(String orderDetails){ 
>		// writes to the DB 
>		orderService.save(orderDetails); 
>		
>		eventPublisher.publishEvent( 
>			new OrderCreatedEvent(this.orderDetails) 
>		); 
>	} 
>}
>```
>Мы хотим, чтобы обработка `OrderCreatedEvent` происходила асинхронно, и если возникает ошибка - похуй.
>Короче - вынести за транзакцию ивыполнить асинхронно.
>




В Spring обработка наследников `ApplicationEvent` **по умолчанию синхронная** и происходит **в том же потоке**, где вызван `publishEvent()` — метод `placeOrder()` будет ждать завершения всех слушателей.

---
## Синхронное поведение по умолчанию


`ApplicationEventMulticaster` (SimpleApplicationEventMulticaster) последовательно вызывает всех `@EventListener`-методов в текущем потоке. Транзакция `@Transactional` не завершится, пока все обработчики события (например, `OrderCreatedEvent`) не выполнятся полностью — это обеспечивает causal consistency для Outbox паттерна.

**~={blue}В данном кейсе:=~**
	*Метод **ждёт** всех `@EventListener`, включая потенциальные side-effects (логи, метрики).*


---
## Асинхронная обработка

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


----
#### [[кейс Асинхронная обработка events - Spring core - Flashcards|Link to flashcards]]



---
### References:

