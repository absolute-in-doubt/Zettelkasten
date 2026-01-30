
07-01-2026 12:39

Status:

Tags: [[Spring]]

---
# Возможности Spring Web


Модуль **Spring Web** — базовый веб‑слой Spring (без MVC), который даёт интеграцию с Servlet API, HTTP‑клиент, конвертацию запросов/ответов и инфраструктуру для построения web‑и REST‑приложений. 

---

## 1. Базовая интеграция с Servlet API и web‑контекстом

- Инициализация IoC‑контейнера Spring в веб‑приложении:
  - слушатели, интеграция с `ServletContext`, web‑ориентированный `ApplicationContext`.
  
- Классы и интерфейсы для построения web‑слоя:
  - пакеты `org.springframework.web.*` (фильтры, обработчики, вспомогательные классы). 

- Поддержка web‑scope’ов (`request`, `session`, `application`) через web‑ориентированный `ApplicationContext`. 

---

## 2. HTTP и REST‑клиент (`RestTemplate` и пр.)

- Класс **`RestTemplate`** (`org.springframework.web.client.RestTemplate`):
  - синхронный HTTP‑клиент высокого уровня для REST‑вызовов (GET/POST/PUT/DELETE и др.);  
  - сериализация/десериализация тел запросов/ответов через `HttpMessageConverter` (JSON, XML и т.п.).
- Инфраструктура для обработки HTTP‑сообщений:
  - `HttpEntity`, `RequestEntity`, `ResponseEntity`;  
  - интерфейсы и реализации `ClientHttpRequestFactory`. 

---

## 3. HttpMessageConverter и работа с телом запросов/ответов

- Абстракция **`HttpMessageConverter`**:
  - преобразование Java‑объектов в HTTP‑тело и обратно (JSON, XML, форматы и т.п.). 

- Используется:
  - на стороне клиента (`RestTemplate`);  
  - на стороне сервера — внутри Spring MVC / WebFlux, но сами базовые конвертеры и контракт живут в `spring-web`. 

---

## 4. Multipart и загрузка файлов

- Поддержка multipart/form‑data:
  - интерфейсы `MultipartFile`, `MultipartResolver` и связанные классы;  
  - абстракция над загрузкой файлов в веб‑формах. 
- Может использоваться как в MVC, так и в собственных сервлетах/фильтрах.

---

## 5. Web‑remoting и интеграция с другими модулями

- Веб‑части remoting‑поддержки Spring:
  - HTTP‑транспорт для удалённых вызовов (например, HTTP invoker).
- Интеграция с другими модулями:
  - подготовка HTTP‑уровня для Spring MVC и Spring WebFlux;  
  - общие утилиты и базовые абстракции для построения web‑сервисов. 

---

## 6. Чем Spring Web отличается от Spring Web MVC

- `spring-web`:
  - базовая веб‑инфраструктура: Servlet‑интеграция, HTTP‑клиент (`RestTemplate`), multipart, HTTP‑сообщения;  
  - **не содержит** `DispatcherServlet`, `@Controller`, `ViewResolver` и т.п. 
- `spring-webmvc`:
  - поверх `spring-web` реализует MVC‑фреймворк: `DispatcherServlet`, `HandlerMapping`, `HandlerAdapter`, `ViewResolver`, `@Controller`, `@RestController` и др. 

То есть **Spring Web — фундаментальный модуль для web/HTTP**, а Spring Web MVC строится над ним как полноценный MVC‑фреймворк. 


---
### References:

