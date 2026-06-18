
19-02-2026 11:58

Status:

Tags: [[Spring Docs (OpenAPI & Swagger)]]

---
# WebMVC-UI configuration properties - Spring OpenAPI docs




By default документация генерируется только при первом заупуске приложения, а дальше кэшируется и хранится вместе с проектом. Если надо, чтобы она каждый раз генерировалась по новой (например активно работаем над API, меняя методы контроллеров), делаем так:

```yml
springdoc:
	cache:
		disabled: true
```


---
### Плное отключение (например для тестов `application-test.yml`):

```yml
springdoc:
  api-docs:
    enabled: false
  swagger-ui:
    enabled: false
```


---
### Springdoc с Spring Security

**специальный режим работы springdoc со Spring Security**:

- Springdoc начинает **учитывать security-аннотации** (`@PreAuthorize`, `@Secured`) в контроллерах
    
- Правильно документирует **security schemes** (JWT, Basic Auth, OAuth2)
    
- Генерирует **Authorize-кнопки** в Swagger UI для тестирования защищенных эндпоинтов
    
- Корректно обрабатывает **фильтры безопасности** при сканировании API

```yml
springdoc:
  enable-spring-security: true
```

---
#### Группировка ендпоинтов по prepath:


```yml
springdoc:
	group-configs:
		- group: strores
		  paths-to-match: /store/**
```


Также это можно делать и программно:

```java
@Bean
public GroupedOpenApi groupOpenApi() {
   String paths[] = {"/v1/**"};
   String packagesToscan[] = {
	   "test.org.springdoc.api.app68.api.user",
	   "test.org.springdoc.api.app68.api.store"
   };
   return GroupedOpenApi.builder()
	   .group("groups")
	   .pathsToMatch(paths)
	   .packagesToScan(packagesToscan)
	   .build();
}
```

Также, при задании групп программно, можно добавлять кастомизацию. Шо цэ и нах надо, пока не ебу. Чекай тут: https://www.youtube.com/watch?v=ondlnm5ZoFM&t=1468s



![[Pasted image 20260219121150.png]]

----
#### [[WebMVC-UI configuration properties - Spring OpenAPI docs - Flashcards|Link to flashcards]]



---
### References:

