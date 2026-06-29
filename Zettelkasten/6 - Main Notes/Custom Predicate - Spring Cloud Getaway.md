
19-06-2026 17:02

Status:

Tags: [[Spring Cloud Getaway]] [[Spring Cloud]] [[Spring]]

---
# Custom Predicate - Spring Cloud Getaway


In order to write a Route Predicate you will need to implement `RoutePredicateFactory` as a bean. There is an abstract class called `AbstractRoutePredicateFactory` which you can extend.

```java
@Component
public class MyRoutePredicateFactory extends AbstractRoutePredicateFactory<MyRoutePredicateFactory.Config> {

    public MyRoutePredicateFactory() {
        super(Config.class);
    }

    @Override
    public Predicate<ServerWebExchange> apply(Config config) {
        // grab configuration from Config object
        return exchange -> {
            //grab the request
            ServerHttpRequest request = exchange.getRequest();
            //take information from the request to see if it
            //matches configuration.
            return matches(config, request);
        };
    }
	
	class Config {
		//Put the configuration properties for your filter here
		boolean isGolden = true;
		private static final String customerIdCookie = "customerId";
	}
}
```

P.S. Переписывал с ебаного Kotlin, можеьт быть хуйня. Не проверял.

----
#### [[Custom Predicate - Spring Cloud Getaway - Flashcards|Link to flashcards]]



---
### References:

