
14-05-2026 08:21

Status:

Tags: [[Resilience4j]] [[System Design]]

---
# Cirquit Breaker implementation - Resilinece4j



```java

// просто строка - имя Cirquit Breaker-а
public static final String USER_SERVICE = "userService"; 


@GetMapping("/displayOrders")
@CircuitBreaker(name =USER_SERVICE,fallbackMethod = "getAllAvailableProducts")
@Retry(name = USER_SERVICE,fallbackMethod = "getAllAvailableProducts")
public List<OrderDTO> displayOrders(@RequestParam("category") String category) {
	String url = category == null ? BASEURL : BASEURL + "/" + category;
	System.out.println("retry method called "+attempt++ +" times "+" at "+new Date());
	return restTemplate.getForObject(url, ArrayList.class);
}


	public List<OrderDTO> getAllAvailableProducts(Exception e){
	return Stream.of(
			new OrderDTO(119, "LED TV", "electronics", "white", 45000),
			new OrderDTO(345, "Headset", "electronics", "black", 7000),
			new OrderDTO(475, "Sound bar", "electronics", "black", 13000),
			new OrderDTO(574, "Puma Shoes", "foot wear", "black & white", 4600),
			new OrderDTO(678, "Vegetable chopper", "kitchen", "blue", 999),
			new OrderDTO(532, "Oven Gloves", "kitchen", "gray", 745)
	).collect(Collectors.toList());
}
```

> Retry “прячет” промежуточные ошибки от CircuitBreaker

> [!warning]
> Cirquit Breaker видит только конечный результат ретраев и регистрирует фейл после ретраев как один невыполненный запрос, поэтому много ретраев здесь нельзя. 
> 
> И, если CirquitBreaker OPEN, то выбросится исключение `CallNotPermittedException`, которое Worker должен обработать.


Application.yaml:
```yaml
resilience4j:
  circuitbreaker:
    instances:
      userService:
        registerHealthIndicator: true
        eventConsumerBufferSize: 10
        failureRateThreshold: 50
        minimumNumberOfCalls: 5
        automaticTransitionFromOpenToHalfOpenEnabled: true
        waitDurationInOpenState: 5s
        permittedNumberOfCallsInHalfOpenState: 3
        slidingWindowSize: 10
        slidingWindowType: COUNT_BASED


  retry:
    instances:
      userService:
        maxRetryAttempts: 5
        waitDuration: 10s
```







----
#### [[Cirquit Breaker implementation - Resilinece4j - Flashcards|Link to flashcards]]



---
### References:

- [[Cirquit Breaker - System Design Patterns]]