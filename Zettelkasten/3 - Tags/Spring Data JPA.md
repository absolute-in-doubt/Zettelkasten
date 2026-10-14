
Index [[Spring Data Access]]: [[Spring]]


[[Common Repository interfaces - Spring Data Access]]
	 JpaRepository - расширяет CrudRepository 
	
- [[setup - Spring Data]]
- [[@Repository - Spring Data]] - @Repository
	- [[existsBy - Spring Data]]
	-  [[Custom repository methods - Spring Data ]]
		- [[Query + native SQL - Spring Data]]
	-  [[@Modifying - Spring Data]]
- [[Service - Spring Data]] - @Service


- [[Entity Auditing - Spring Data JPA]]
- [[EntityListeners - Spring Data JPA]]
- [[AuditorAware - Spring Data JPA]]

Фильтрация:
- [[Specification - Spring Data]] 
Пагинация:
- [[Пагинация- Spring Data]]
	- [[@PageableDefault - Spring Data]]


```yaml
spring:  
  application:  
    name: userservice  
 
  jpa:  
    hibernate:  
      ddl-auto: validate  
    properties:  
      hibernate:  
        connection:  
          isolation: READ_COMMITTED  
        dialect: org.hibernate.dialect.PostgreSQLDialect  
        show_sql: false  
  datasource:  
    url: ${POSTGRES_URL}  
    username: ${POSTGRES_USER}  
    password: ${POSTGRES_PASSWORD}  
  liquibase:  
    change-log: classpath:db/changelog/db.changelog-master.yaml  
    enabled: true
```

---

- [[Spring ORM]]

- [[Hibernate & JPA]]