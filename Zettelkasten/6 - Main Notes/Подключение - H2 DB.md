
07-01-2026 10:18

Status:

Tags:

---
# Подключение - H2 DB

Для начала импортируем саму H2 (идёт сразу вместе с JDBC драйвером)

```xml
<!-- https://mvnrepository.com/artifact/com.h2database/h2 -->
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <version>2.4.240</version>
    <scope>test</scope>
</dependency>
```

> [!note]
> в `spring-boot-starter-test` H2 не идёт по умолчанию. Её надо вручную импортировать.



### Режимы работы H2

- In‑memory (для тестов, живёт пока работает приложение):
	
    `jdbc:h2:mem:testdb`
    
    Часто добавляют опции, чтобы БД не закрывалась между коннектами:
	
    
    `jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE` - ~={orange}**БД существует весь запуск приложения/тестового процесса и исчезает только при завершении JVM**. Между разными запусками `mvn test` она создаётся заново и каждый раз чистая.=~
	

- Файловая БД (хранится на диске):
    
    `jdbc:h2:file:./data/mydb`
    
    или с абсолютным путём:
    
    `jdbc:h2:file:/path/to/db/mydb`
	

- TCP‑режим (к H2 как к серверу):
    
    `jdbc:h2:tcp://localhost:9092/~/mydb`
	


### Пример настроек подключения

```properties
jdbc.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
jdbc.username=sa
jdbc.password=
jdbc.driverClassName=org.h2.Driver
```


### Дальнейшие настройки - через Hibernate

Пример:


----
#### [[Подключение - H2 DB - Flashcards|Link to flashcards]]



---
### References:

