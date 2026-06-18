
16-06-2026 10:54

Status:

Tags: [[Testing]]

---
# WireMock


## **1. Введение в WireMock**

**WireMock** — это библиотека для мокинга HTTP-сервисов. Она позволяет:

- Эмулировать внешние API в тестах.
- Проверять исходящие HTTP-запросы.
- Настраивать ожидаемые ответы (stubbing).
- Записывать и воспроизводить реальные HTTP-взаимодействия (record/playback).

---

## **2. Подключение зависимостей**

Добавьте в `pom.xml` (Maven):

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-contract-wiremock</artifactId>
    <version>4.0.4</version> <!-- Проверьте актуальную версию -->
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>com.github.tomakehurst</groupId>
    <artifactId>wiremock-jre8</artifactId>
    <version>2.35.0</version> <!-- Проверьте актуальную версию -->
    <scope>test</scope>
</dependency>
```

 **`wiremock-jre8`**

- Это **основная библиотека WireMock** — она содержит весь функционал для мокинга HTTP-сервисов (stubbing, verification, record/playback и т.д.).
- **Самостоятельная**: её достаточно, чтобы использовать WireMock в любом Java-проекте, не только в Spring.


**`spring-cloud-contract-wiremock`**

- Это **обёртка от Spring Cloud**, которая **интегрирует WireMock в Spring-экосистему**.
- Она предоставляет удобные аннотации и автоконфигурацию для Spring Boot-приложений, например:
    - `@AutoConfigureWireMock`
    - `@AutoConfigureStubRunner`
- **Не обязательна**, если ты готов настроить WireMock вручную.


---
## **3. Базовая настройка WireMock в Spring Test**

### **3.1. Запуск WireMock-сервера**

Используйте аннотацию `@AutoConfigureWireMock` для автоматической настройки сервера в тестах:

```java
@SpringBootTest
@AutoConfigureWireMock(port = 0) // Случайный порт
public class MyServiceTest {
    // ...
}
```

- `port = 0` — сервер запустится на случайном свободном порту.
- По умолчанию WireMock будет доступен на `http://localhost:<port>`.

---

### **3.2. Настройка stub-ов (заглушек)**

Создайте заглушки для эмуляции ответов от внешних сервисов.

**Пример:**

```java
@Test
void testExternalApiCall() {
    // Настройка stub для GET-запроса
    stubFor(
        get(urlEqualTo("/api/data"))
            .willReturn(
                aResponse()
                    .withStatus(200)
                    .withHeader("Content-Type", "application/json")
                    .withBody("{\"message\": \"Hello, WireMock!\"}")
            )
    );

    // Вызов тестируемого метода, который обращается к внешнему API
    String result = myService.callExternalApi();

    assertEquals("Hello, WireMock!", result);
}
```

> [!warning]
> Не забывайте очищать stub-ы между тестами (`resetAllScenarios()`).


---

## **4. Проверка исходящих запросов**

WireMock позволяет проверять, что ваш код отправляет ожидаемые запросы.

**Пример:**

```java
@Test
void testOutgoingRequest() {
    // Настройка stub
    stubFor(
        post(urlEqualTo("/api/save"))
            .willReturn(aResponse().withStatus(201))
    );

    // Вызов метода, который отправляет POST-запрос
    myService.saveData("test");

    // Проверка, что запрос был отправлен
    verify(
        postRequestedFor(urlEqualTo("/api/save"))
            .withHeader("Content-Type", equalTo("application/json"))
            .withRequestBody(equalToJson("{\"data\": \"test\"}"))
    );
}
```

---

## **5. Работа с JSON**

Для удобной работы с JSON используйте `equalToJson` или `matchingJsonPath`.

**Пример:**

```java
stubFor(
    post(urlEqualTo("/api/users"))
        .withRequestBody(equalToJson("{\"name\": \"John\", \"age\": 30}"))
        .willReturn(aResponse().withStatus(200))
);
```

---

## **6. Динамические ответы**

Можно настраивать динамические ответы в зависимости от запроса.

**Пример:**

```java
stubFor(
    get(urlPathEqualTo("/api/users/{id}"))
        .willReturn(
            aResponse()
                .withStatus(200)
                .withBody("{\"id\": \"{id}\", \"name\": \"User {id}\"}")
        )
);
```

---

## **7. Запись и воспроизведение (Record/Playback)**

WireMock поддерживает запись реальных запросов и их воспроизведение в тестах.

**Пример:**

```java
@BeforeEach
void setup() {
    // Включение режима записи
    wireMockServer.startRecording("http://real-api.example.com");
}

@AfterEach
void tearDown() {
    // Сохранение записанных взаимодействий в файл
    wireMockServer.stopRecording();
    wireMockServer.saveMappings();
}
```

---

## **8. Интеграция с Spring Cloud Contract**

Если используете **Spring Cloud Contract**, WireMock можно интегрировать для тестирования контрактов.

**Пример:**

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureStubRunner
public class ContractTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void testContract() {
        String result = restTemplate.getForObject(
            "/api/contract",
            String.class
        );
        assertEquals("Expected response", result);
    }
}
```

---



----
#### [[WireMock - Flashcards|Link to flashcards]]



---
### References:

