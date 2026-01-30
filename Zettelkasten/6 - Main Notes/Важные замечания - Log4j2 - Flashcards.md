
Theory for the cards: [[Важные замечания - Log4j2]]

FILE TAGS: logging java_interview

Q: В чем отличие Log4j2 от Log4j?
A: 1. В Log4j2 присутствует возможность асинхоронного логгирования:
	
(Включается в настройках.: добавляем asyncLogger вместо обычного):
```java
<Loggers> 
	<AsyncLogger name="com.example" level="DEBUG" additivity="false"> 
		<AppenderRef ref="Console"/>
		<AppenderRef ref="FileAppender"/> 
	</AsyncLogger> 
	<Root level="INFO"> 
		<AppenderRef ref="Console"/> 
	</Root> 
</Loggers>
```
	
	
---
2. Lazily логгирование с помощью `Supplier<?>`
	
Методы Logger, принимающие `Supplier<?>` выполняются только в случае, если включён соответствующий уровень логгирования.
	
```java
logger.debug(() -> "All the products found by the category " + category.getName() + ":\n"+  query.getResultList());
```
	
Например такое сообщение будет интерпретировано только если для этого логгера включён `DEBUG` (или более «подробный» `TRACE`).
<!--ID: 1769416804710-->
