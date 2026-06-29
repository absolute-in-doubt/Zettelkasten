
19-06-2026 16:39

Status:

Tags: [[overview - Spring Cloud Getaway]] [[Spring Cloud]] [[Spring]]

---
# Конфигурация - Spring Cloud Getaway

![[Pasted image 20260619164041.png]]

Здесь:

- `id` — уникальный идентификатор маршрута.

- `uri` — целевой сервис.
   
- `predicates` — условия срабатывания маршрута. ([[Predicates configuration - Spring Cloud Getaway|Predicates configuration]])

- `filters`  —  фильтры для данного `route` ([[Filters configuration - Spring Cloud Getaway]])

> [!warning]
> URIs defined in routes without a port get default port values of 80 and 443 for the HTTP and HTTPS URIs, respectively.


В Spring Cloud Gateway `routes` — это сердце конфигурации. Каждый route описывает:

1. **Когда** маршрут должен сработать (`predicates`)
    
2. **Куда** перенаправить запрос (`uri`)
    
3. **Что сделать с запросом/ответом** (`filters`) 
    


---
## Использование Eureka / Service Discovery

Чаще всего в микросервисах используют:

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: user-service
          uri: lb://USER-SERVICE
          predicates:
            - Path=/users/**
```

Здесь:

- `lb://` означает использование Spring Cloud LoadBalancer.
    
- `USER-SERVICE` — имя сервиса в Eureka или Consul.
    

Gateway найдёт зарегистрированный инстанс и перенаправит запрос туда. 


---
## Пример конфигурации с Eureka

```yaml
spring:
  cloud:
    gateway:
      routes:

        - id: auth-service
          uri: lb://AUTH-SERVICE
          predicates:
            - Path=/auth/**
          filters:
            - StripPrefix=1

        - id: order-service
          uri: lb://ORDER-SERVICE
          predicates:
            - Path=/orders/**
          filters:
            - StripPrefix=1

        - id: user-service
          uri: lb://USER-SERVICE
          predicates:
            - Path=/users/**
          filters:
            - StripPrefix=1
```

Тогда:

```http
GET /users/profile
```

превратится в

```http
GET /profile
```

и уйдёт в USER-SERVICE.

---

# Shortcut и Expanded syntax

Spring поддерживает две формы записи.

Короткая:

```yaml
predicates:
  - Path=/users/**
```

Полная:

```yaml
predicates:
  - name: Path
    args:
      pattern: /users/**
```

То же самое касается фильтров. 

---

# Частый вопрос на интервью

Что произойдёт, если два route подходят под один запрос?

```yaml
- id: route1
  predicates:
    - Path=/users/**

- id: route2
  predicates:
    - Path=/users/profile/**
```

Gateway выберет маршрут с более высоким приоритетом (`order`) или наиболее специфичный маршрут после построения таблицы маршрутов. Для явного контроля можно задавать:

```yaml
- id: route1
  order: 10

- id: route2
  order: 1
```

Меньшее значение `order` означает более высокий приоритет.



----
#### [[Конфигурация - Spring Cloud Getaway - Flashcards|Link to flashcards]]



---
### References:

