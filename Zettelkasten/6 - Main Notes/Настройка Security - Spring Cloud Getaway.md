
19-06-2026 17:53

Status:

Tags: [[Spring Cloud Getaway]] [[Spring Cloud]] [[Spring]]

---
# Настройка Security - Spring Cloud Getaway

Spring Cloud Gateway работает на **Spring WebFlux**, а не на Servlet API.

Поэтому используются:

```java
ServerHttpSecurity
SecurityWebFilterChain
```

а не:

```java
HttpSecurity
SecurityFilterChain
```



> [!note] 
> Но используем обычный resource server dependency:
> ```xml
> <dependency>  
>	<groupId>org.springframework.boot</groupId>  
>	<artifactId>spring-boot-starter-oauth2-resource-server</artifactId>  
></dependency>
> ```


---

## Конфигурация через application.yaml

Самый простой вариант — если есть Authorization Server с JWKS endpoint:

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: "http://localhost:9000"
```

или

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          jwk-set-uri: "http://localhost:9000/oauth2/jwks"
```

После этого Spring автоматически создаст:

```java
ReactiveJwtDecoder
```

и настроит JWT-проверку.


---
## SecurityWebFilterChain

Минимальная конфигурация:

```java
@Configuration
@EnableWebFluxSecurity
public class SecurityConfig {

    @Bean
    SecurityWebFilterChain securityWebFilterChain(ServerHttpSecurity http) {
        return http
            .csrf(ServerHttpSecurity.CsrfSpec::disable)
			
            .authorizeExchange(exchanges -> exchanges
                .pathMatchers("/auth/**").permitAll()
                .anyExchange().authenticated()
            )
			
            .oauth2ResourceServer(oauth2 ->
                oauth2.jwt(Customizer.withDefaults())
            )
			
            .build();
    }
}
```

Здесь:

```java
.oauth2ResourceServer(...)
```

добавляет все необходимые фильтры автоматически.

---

## Какие фильтры появляются при настройке `oauth2ResourceServer`

В реактивном стеке будет добавлен:

```java
AuthenticationWebFilter
```

внутри которого используется `ServerBearerTokenAuthenticationConverter` для извлечения Bearer Token.

Далее `JwtReactiveAuthenticationManager` валидирует JWT.

Схематично:

```text
Authorization Header
        |
        v
AuthenticationWebFilter
        |
        v
JwtReactiveAuthenticationManager
        |
        v
ReactiveJwtDecoder
        |
        v
SecurityContext
```

Это полный аналог цепочки фильтров Resource Server из Servlet Security.

---

## Можно ли использовать как GlobalFilter?

Технически можно:

```java
@Component
public class JwtGlobalFilter implements GlobalFilter {
    ...
}
```

Но обычно это плохая идея.

Причины:

### 1. Дублирование Spring Security

Тебе придется самому:

- извлекать Bearer Token;
    
- валидировать подпись;
    
- проверять exp;
    
- проверять nbf;
    
- создавать Authentication;
    
- класть его в SecurityContext.
    

Все это уже умеет Resource Server.

---

### 2. Потеря интеграции

Не будут работать:

```java
@PreAuthorize
hasRole(...)
hasAuthority(...)
```

если ты корректно не создашь Authentication.

---

### 3. Порядок фильтров

Gateway имеет свои:

```java
GlobalFilter
GatewayFilter
```

а Spring Security имеет:

```java
WebFilter
```

Это разные цепочки.

Можно легко получить ситуацию:

```text
GlobalFilter
    |
    v
Routing
    |
    v
Security
```

или наоборот.

Поэтому обычно аутентификацию держат именно в Security.


---
## Проверка ролей по маршрутам

Например:

```java
.authorizeExchange(exchange -> exchange
    .pathMatchers("/admin/**").hasRole("ADMIN")
    .pathMatchers("/user/**").authenticated()
    .anyExchange().permitAll()
)
```

JWT:

```json
{
  "sub": "john",
  "roles": ["ADMIN"]
}
```

через `JwtAuthenticationConverter` можно преобразовать в:

```java
ROLE_ADMIN
```

и ограничения начнут работать автоматически.

---

## Если Gateway только проверяет токен

Очень распространенный вариант:

```text
Client
   |
   v
Gateway (Resource Server)
   |
   v
Microservice
```

Микросервисы доверяют Gateway и уже не валидируют JWT.

Но сейчас чаще встречается схема:

```text
Client
   |
   v
Gateway (Resource Server)
   |
   v
User Service (Resource Server)
Order Service (Resource Server)
```

То есть токен валидируется дважды:

1. На Gateway — для раннего отсечения невалидных запросов.
    
2. В каждом сервисе — для защиты от обхода Gateway.
    

---

## Что обычно спрашивают на интервью

Если спросят:

> Как валидировать JWT в Spring Cloud Gateway?

Ожидаемый ответ:

> Gateway работает на WebFlux, поэтому я подключаю `spring-boot-starter-oauth2-resource-server`, настраиваю `SecurityWebFilterChain` и включаю `.oauth2ResourceServer().jwt()`. Spring добавляет реактивный `AuthenticationWebFilter`, который извлекает Bearer Token и валидирует его через `ReactiveJwtDecoder`. Использовать `GlobalFilter` для проверки JWT обычно не нужно, потому что это уже решается Spring Security. Для дополнительной логики можно написать собственный `ReactiveAuthenticationManager` или `JwtAuthenticationConverter`.

Это считается каноническим подходом для Spring Cloud Gateway.


----
#### [[Настройка Security - Spring Cloud Getaway - Flashcards|Link to flashcards]]



---
### References:

- [[Spring Security]]