
28-09-2026 13:33

Status:

Tags: [[Networks]] [[Java+]]

---
# SOAP protocol



### 1. Что такое SOAP

**SOAP (Simple Object Access Protocol)** — протокол обмена структурированными сообщениями между системами, обычно поверх **HTTP**, где формат сообщения — **XML**.

Главная идея:

```text
Client
  │
  │ HTTP POST + XML/SOAP
  ▼
SOAP Server
  │
  └── Java Service
```

В отличие от REST, SOAP имеет строгий **контракт**, обычно описанный через **WSDL**.

---

### 2. SOAP-сообщение

Типичная структура:

```xml
<soap:Envelope>
    <soap:Header>
        <!-- metadata, security, transaction и т.д. -->
    </soap:Header>

    <soap:Body>
        <getUser>
            <userId>123</userId>
        </getUser>
    </soap:Body>
</soap:Envelope>
```

Основные части:

- **Envelope** — корневой элемент SOAP-сообщения.
    
- **Header** — необязательные метаданные: security, authentication и т.п.
    
- **Body** — непосредственно запрос/ответ.
    
- **Fault** — стандартный формат SOAP-ошибки.
    

Пример ответа:

```xml
<soap:Body>
    <getUserResponse>
        <user>
            <id>123</id>
            <name>John</name>
        </user>
    </getUserResponse>
</soap:Body>
```

---

### 3. WSDL — контракт

**WSDL (Web Services Description Language)** описывает SOAP API:

```text
WSDL
 ├── operations
 ├── request/response
 ├── data types
 └── endpoint
```

Условно:

```xml
<operation name="getUser">
    request  → GetUserRequest
    response → GetUserResponse
</operation>
```

Типы данных обычно описываются через **XSD**:

```xml
<xs:complexType name="GetUserRequest">
    <xs:sequence>
        <xs:element name="userId" type="xs:long"/>
    </xs:sequence>
</xs:complexType>
```

То есть:

```text
WSDL
  └── XSD
       ├── Request types
       ├── Response types
       └── domain types
```

---

### 4. Как это выглядит в Java

В Java обычно не пишут XML вручную. **Клиент или серверные классы генерируются из WSDL/XSD.**

Например, через **JAX-WS / Apache CXF**:

```text
WSDL
  ↓
wsdl2java
  ↓
Java classes
  ├── DTO
  ├── Service interface
  └── JAXB classes
```

Например:

```java
@WebService
public interface UserService {

    GetUserResponse getUser(GetUserRequest request);
}
```

На сервере реализация:

```java
@WebService(endpointInterface = "com.example.UserService")
public class UserServiceImpl implements UserService {

    @Override
    public GetUserResponse getUser(GetUserRequest request) {
        // business logic
    }
}
```

**Apache CXF** — один из наиболее распространённых инструментов для SOAP в Java; умеет генерировать Java-код из WSDL и публиковать SOAP endpoints.

---

### 5. SOAP Client

Аналогично клиент можно сгенерировать из контракта:

```text
WSDL
 ↓
wsdl2java
 ↓
Java client/proxy
 ↓
userService.getUser(...)
 ↓
SOAP XML
 ↓
HTTP
```

То есть разработчик работает примерно так:

```java
GetUserResponse response =
        userService.getUser(request);
```

а XML и HTTP/SOAP-детали скрыты библиотекой.

---

### 6. SOAP vs REST — главное

|SOAP|REST|
|---|---|
|Протокол|Архитектурный стиль|
|XML|JSON чаще всего|
|WSDL — строгий контракт|OpenAPI часто используется как контракт|
|`POST` обычно используется для операций|GET/POST/PUT/DELETE|
|WS-* стандарты|Обычно проще|
|Часто enterprise/legacy/banking|Часто современные Web API|
|CXF, JAX-WS|Spring MVC/WebFlux|

**Для Java-интервью главное:**

> SOAP — контрактный XML-протокол. **WSDL описывает сервис**, XSD — структуры данных, а в Java **Apache CXF/JAX-WS** позволяют генерировать клиентский/серверный код из WSDL и работать с SOAP через обычные Java-интерфейсы.

----
#### [[SOAP protocol - Flashcards|Link to flashcards]]



---
### References:

