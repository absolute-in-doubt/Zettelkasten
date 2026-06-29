
19-06-2026 17:10

Status:

Tags: [[Spring Cloud Getaway]] [[Spring Cloud]] [[Spring]]

---
# Настройка логгирования и трейсинга - Spring Cloud Getaway

```yaml
spring:
	application:
		name: "api-getaway"
	cloud:
		getaway:
			httpserver:
				wiretap: true
			httpclient:
				wiretap: true
				
logging:
	level:
		org.springframework.cloud.getaway: DEBUG
		org.springframework.http.server.reactive: DEBUG
		org.springframework.web.reactive: DEBUG
		org.springframework.boot.autoconfigure.web: DEBUG
		reactor.netty: DEBUG 
```

----
#### [[Настройка логгирования и трейсинга - Spring Cloud Getaway - Flashcards|Link to flashcards]]



---
### References:

- [[Конфигурация spring-jcl - Spring Boot]]