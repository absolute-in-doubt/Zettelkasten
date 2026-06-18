
27-05-2026 12:17

Status:

Tags: [[Spring Test]] [[Testing]]

---
# TestRestTemplate - Spring Test


`TestRestTemplate` — настоящий HTTP клиент для integration tests.

Он:

- запускает приложение полностью
    
- поднимает embedded server
    
- делает реальные HTTP requests
    

---

# Как работает

```text
TestRestTemplate
    ↓ REAL HTTP
Tomcat/Jetty
    ↓
DispatcherServlet
    ↓
Controller
    ↓
Service
    ↓
Repository
    ↓
	DB
```

Это уже почти E2E test внутри приложения.

---

### Что тестирует

- настоящий HTTP
    
- embedded server
    
- serialization
    
- filters
    
- security
    
- headers
    
- cookies
    
- network layer
    

---

### Когда использовать

Когда нужен настоящий integration test.

Обычно:

```java
@SpringBootTest(webEnvironment = RANDOM_PORT)
```

---

### Пример TestRestTemplate

#### Тест

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class UserControllerIT {

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldReturnUser() {

        ResponseEntity<UserDto> response =
                restTemplate.getForEntity(
                        "/users/1",
                        UserDto.class
                );

        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals("John", response.getBody().name());
    }
}
```

---

### Как это работает внутри

Spring:

- запускает Tomcat
    
- выбирает случайный порт
    
- inject'ит настроенный HTTP client
    

`TestRestTemplate` делает реальный HTTP call.


---
### Преимущества TestRestTemplate

- Максимально близко к production
	
-  Тестирует настоящий HTTP stack
	
- Хорошо подходит для:
	
	- security tests
	    
	- cookie/header tests
	    
	- full integration tests


### Недостатки TestRestTemplate

- Медленнее MockMvc

	Потому что:
	
	- сервер реально запускается
	    
	- используется networking
    



----
#### [[TestRestTemplate - Spring Test - Flashcards|Link to flashcards]]



---
### References:

