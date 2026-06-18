
Index: [[Java+]] [[System Design]]


![[Pasted image 20260514082146.png]]


- [[Cirquit Breaker implementation - Resilinece4j]]
	- [[Cirquit Breaker - System Design Patterns]]


- [[Retry mechanism implementation - Resilience4j]]

- [[Time Limiter - Resilience4j]]

**~={red}?!=~** Модуль `resilience4j-cache` встречается крайне редко. Даже автор библиотеки писал, что для Spring-приложений обычно достаточно Spring Cache, а `resilience4j-cache` создавался под конкретный сценарий с JCache/Hazelcast и обработкой ошибок кеша.


----
### Dependencies

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-aop</artifactId>
</dependency>
<dependency>
	<groupId>io.github.resilience4j</groupId>
	<artifactId>resilience4j-spring-boot2</artifactId>
	<version>1.5.0</version>
</dependency>
```

- aop - нужен, чтобы отслеживать статус Cirquit Breaker и предоставлять актуатору

- actuator - чтобы отображать данные на эндпоинте **
