
08-01-2026 10:24

Status:

Tags: [[Log4j2]] [[Logging]] [[Java+]]

---
# Важные замечания - Log4j2

Log4j2 - улучшенная версия Log4j. Главное нововведение - ~={cyan}асинхронное логирование=~.
Но без добавления LMAX он все ещё синхрнонный.

### Требуемые зависимости

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.25.3</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.25.3</version>
</dependency>
```

~={cyan}Для асинхронности:=~

```xml
<dependency>
    <groupId>com.lmax</groupId>
    <artifactId>disruptor</artifactId>
    <version>3.3.4</version>
</dependency>
```


---
## Особенности Log4j2


### Lazily логгирование с помощью `Supplier<?>`

Методы Logger, принимающие `Supplier<?>` выполняются только в случае, если включён соответствующий уровень логгирования.

```java
logger.debug(() -> "All the products found by the category " + category.getName() + ":\n"+  query.getResultList());
```

Например такое сообщение будет интерпретировано только если для этого логгера включён `DEBUG` (или более «подробный» `TRACE`).


---
### [[Важные замечания - Log4j2 - Flashcards|Link to the Flashcards]]

---
### References:

