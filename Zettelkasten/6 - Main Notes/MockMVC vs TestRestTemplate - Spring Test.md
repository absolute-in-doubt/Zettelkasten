
27-05-2026 12:20

Status:

Tags: [[Spring Test]]

---
# MockMVC vs TestRestTemplate - Spring Test


| |MockMvc|TestRestTemplate|
|---|---|---|
|Реальный HTTP|❌|✅|
|Запускает сервер|❌|✅|
|Скорость|Очень быстро|Медленнее|
|Уровень|MVC layer|Full HTTP|
|Хорош для|Controller tests|Full integration|
|Networking|Нет|Да|

---

### Что чаще используют

#### MockMvc

Самый популярный вариант для backend integration tests.

Особенно:

```java
@SpringBootTest
@AutoConfigureMockMvc
```

---

#### TestRestTemplate

Используют:

- реже
    
- когда нужен именно настоящий HTTP
    

---

### Современная альтернатива — WebTestClient

В новых проектах часто используют:

WebTestClient

Он:

- современнее
    
- reactive-friendly
    
- может работать и как MockMvc, и как реальный HTTP client
    

Но для обычного Spring MVC:

- MockMvc всё ещё стандарт де-факто.
    

---

# Что выбрать

## MockMvc

Если нужно:

- быстро
    
- удобно
    
- тестировать controller/API
    

Обычно лучший выбор.

---

## TestRestTemplate

Если нужно:

- настоящий HTTP
    
- тестирование security/network behavior
    
- максимально production-like tests
    

---

# Типичная стратегия

## Unit tests

```text
Service + Mockito
```

---

## Controller integration tests

```text
MockMvc + Testcontainers
```

---

## Несколько full HTTP tests

```text
TestRestTemplate
```

для critical scenarios/security/auth.

----
#### [[MockMVC vs TestRestTemplate - Spring Test - Flashcards|Link to flashcards]]



---
### References:

