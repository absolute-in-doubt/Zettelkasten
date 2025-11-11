
09-11-2025 09:43

Status:

Tags: [[Spring]]

---
# application.properties - Spring Boot

Автоматически считывается Spring Boot-ом.

Нужен для его настройки.


```java
//стандартные ключи, считываемые Spring Boot-ом
server.port=7070

spring.datasource.url=jdbc:postgres://localhost:5432/mydb

//пользовательские ключи (не стандартизированы Spring Boot-ом). Их читаем и создаём мы
hello=world

```

## Пример реальной конфигурации

```java
spring.application.name=spring.boot.app_2  

//конфигурация DataSource
spring.datasource.driver-class-name=org.postgresql.Driver  
spring.datasource.url=jdbc:postgresql://localhost:5432/project_1  
spring.datasource.username=postgres  
spring.datasource.password=root  
  
//конфигурация самого Hibernate (можем указать в этом же файле, т.к. к этому файлу Hibernate не имеет доступа. Его читает сам Spring)
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect  
spring.jpa.properties.hibernate.show_sql=true
```

#### Добавление фильтра к Dispatcher servlet 
**Одной строкой в application.properties!**

```java
spring.mvc.hiddenmethod.filter.enabled=true
```


----
#### [[application.properties - Spring Boot - Flashcards|Link to flashcards]]



---
### References:

- [[Filter - Spring]]