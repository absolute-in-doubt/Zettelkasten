
19-06-2026 16:52

Status:

Tags: [[Spring Cloud Getaway]] [[Spring Cloud]] [[Spring]]

---
# Predicates configuration - Spring Cloud Getaway




### `Path`

Например:

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: user-service
          uri: http://localhost:8081
          predicates:
            - Path=/users/**
```

Запрос:

```http
GET /users/123
```

будет проксирован на:

```http
http://localhost:8081/users/123
```


> [!Note]
> Здесь, в первой строке `predicates`, объявлен `Path=..`. Эт настройка конфигурирует Path Route Predicate Factory.


Path predicate выбирает по специфичности маршрутов. Но лучше добавить порядок проверки routes:

```yaml
spring:  
  application:  
    name: apigateway  
  cloud:  
    gateway:  
      routes:  
        - id: user-service  
          order: 10  
          uri: ${USER_SERVICE_BASE_URI}  
          predicates:  
            - Path=/api/v1/users/**,/api/v1/cards/**  
        - id: auth-service  
          order: 2  
          uri: ${AUTH_SERVICE_BASE_URI}  
          predicates:  
            - Path=/api/v1/auth/**  
        - id: order-service  
          order: 1  
          uri: ${ORDER_SERVICE_BASE_URI}  
          predicates:  
            - Path=/api/v1/orders/**,/api/v1/users/**/orders
```

`order` — это **приоритет маршрута** (`Route`).

Когда один и тот же запрос подходит под несколько маршрутов, Spring Cloud Gateway сортирует их по `order` и проверяет в этом порядке.

~={red}**меньшее значение = более высокий приоритет**.=~


---
### По HTTP-методу

```yaml
predicates:
  - Method=GET,POST
```

Сработает только для GET и POST запросов. 


---
### По заголовку

```yaml
predicates:
  - Header=X-Request-Id,\d+
```

Сработает только если есть заголовок:

```http
X-Request-Id: 12345
```


---
### По query параметру

```yaml
predicates:
  - Query=version,v1
```

Подойдёт запрос:

```http
GET /users?version=v1
```


---
### По Cookie

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: after_route
        uri: https://example.org
        predicates:
        - Cookie=mycookie,mycookievalue
```

The previous sample defines the `Cookie` Route Predicate Factory with two arguments, the cookie name, `mycookie` and the value to match `mycookievalue`.


----
## Несколько predicates

Все predicates объединяются через AND.

```yaml
predicates:
  - Path=/users/**
  - Method=GET
```

Эквивалентно:

```java
pathMatches && methodMatches
```





---
### Пример Extended syntax config в `application.yaml`

Fully expanded arguments appear more like standard yaml configuration with name/value pairs. Typically, there will be a `name` key and an `args` key. The `args` key is a map of key value pairs to configure the predicate or filter.

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: after_route
        uri: https://example.org
        predicates:
        - name: Cookie
          args:
            name: mycookie
            regexp: mycookievalue
```

This is the full configuration of the shortcut configuration of the `Cookie` predicate shown above.


### [Куча Spring Cloud Getaway default Predicates](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/?utm_source=chatgpt.com#gateway-request-predicates-factories)


----
#### [[Predicates configuration - Spring Cloud Getaway - Flashcards|Link to flashcards]]



---
### References:

- [[Конфигурация - Spring Cloud Getaway]]