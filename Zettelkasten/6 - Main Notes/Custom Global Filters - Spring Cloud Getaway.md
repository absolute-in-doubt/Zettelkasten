
19-06-2026 17:34

Status:

Tags: [[Spring Cloud Getaway]] [[Spring Cloud]] [[Spring]]

---
# Custom Global Filters - Spring Cloud Getaway


To write a custom global filter, you must implement `GlobalFilter` interface as a bean. **~={red}This applies the filter to all requests.=~**

The following examples show how to set up global pre- and post-filters, respectively:

```java
@Bean
public GlobalFilter customGlobalFilter() {
    return (exchange, chain) -> exchange.getPrincipal()
        .map(Principal::getName)
        .defaultIfEmpty("Default User")
        .map(userName -> {
          //adds header to proxied request
          exchange.getRequest()
          .mutate()
          .header("CUSTOM-REQUEST-HEADER", userName).build();
          return exchange;
        })
        .flatMap(chain::filter);
}

@Bean
public GlobalFilter customGlobalPostFilter() {
    return (exchange, chain) -> chain.filter(exchange)
        .then(Mono.just(exchange))
        .map(serverWebExchange -> {
          //adds header to response
          serverWebExchange.getResponse().getHeaders()
          .set("CUSTOM-RESPONSE-HEADER",
              HttpStatus.OK
              .equals(serverWebExchange.getResponse().getStatusCode()
              ) ? "It worked": "It did not work");
          return serverWebExchange;
        })
        .then();
}


```


----
#### [[Custom Global Filters - Spring Cloud Getaway - Flashcards|Link to flashcards]]



---
### References:

