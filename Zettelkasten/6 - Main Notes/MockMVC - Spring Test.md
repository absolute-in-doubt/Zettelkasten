
27-05-2026 12:13

Status:

Tags: [[Spring Test]] [[Testing]]

---
# MockMVC - Spring Test


Оба инструмента используются для тестирования HTTP API в Spring Boot приложениях, но работают на разных уровнях.


---
## MockMvc

`MockMvc` — инструмент для тестирования Spring MVC без запуска реального HTTP-сервера.

Он:

- НЕ открывает порт
    
- НЕ делает реальные HTTP-запросы
    
- работает внутри Spring MVC pipeline
    

Фактически Spring имитирует HTTP-запрос внутри JVM.

---

## Как работает

```text
MockMvc.perform(...)
        ↓
DispatcherServlet
        ↓
Controller
        ↓
Service
        ↓
Repository
```

Но:

- Tomcat НЕ запускается
    
- сеть НЕ используется
    

---

## Что тестирует

- Controller
    
- request mapping
    
- validation
    
- serialization/deserialization
    
- exception handlers
    
- filters/interceptors
    
- Spring MVC конфигурацию
    

### Не тестирует:

- реальную сеть
    
- работу embedded server
    
- реальные HTTP timeout/network cases
    

---

### Когда использовать

#### 1. Controller unit/integration tests

Очень часто:

```java
@WebMvcTest(UserController.class)
```

с моками сервисов.

---

#### 2. Full integration tests

```java
@SpringBootTest
@AutoConfigureMockMvc
```

с настоящей БД.

---

### Пример MockMvc

#### Controller

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{id}")
    public UserDto getUser(@PathVariable Long id) {
        return new UserDto(id, "John");
    }
}
```


#### Тест

```java
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;

import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest
@AutoConfigureMockMvc
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void shouldReturnUser() throws Exception {

        mockMvc.perform(get("/users/1"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.id").value(1))
                .andExpect(jsonPath("$.name").value("John"));
    }
}
```


---
### Примеры других запросов

##### POST

```java
mockMvc.perform(
        post("/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content("""
                    {
                      "name": "John"
                    }
                """)
)
```


##### PATCH

```java
mockMvc.perform(
        put("/users/1")
                .contentType(MediaType.APPLICATION_JSON)
                .content("""
                    {
                      "name": "Updated"
                    }
                """)
)
```

---

### Основные методы MockMvc

#### perform

Выполняет запрос:

```java
mockMvc.perform(get("/users"))
```

---

#### andExpect

Проверка результата:

```java
.andExpect(status().isOk())
```

---

#### jsonPath

Проверка JSON:

```java
.andExpect(jsonPath("$.name").value("John"))
```

---

### Преимущества MockMvc

- Очень быстрый: нет реальных HTTP запросов
	
- Отлично подходит для controller tests
	
-  Хорошо интегрирован со Spring MVC
	
- Позволяет точно проверять JSON

### Недостатки MockMvc


- Не настоящий HTTP: это имитация.

- Не тестирует:
	
	- реальный networking
	    
	- HTTP stack целиком
	    
	- embedded server



----
#### [[MockMVC vs TestRestTemplate - Spring Test - Flashcards|Link to flashcards]]



---
### References:

