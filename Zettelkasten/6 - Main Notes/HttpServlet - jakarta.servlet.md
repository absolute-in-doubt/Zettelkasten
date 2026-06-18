
19-10-2025 07:31

Status: #baby 

Tags: [[Jakarta.servlet]] [[Java+]]

---
# HttpServlet - jakarta.servlet

(abstract class)

Создаётя ОДИН на все потоки обработки Http запросов Servlet-контейнера (даже user-defined HttpServlet).

> [!warning] **Все создаваемые переменные в самом HttpServlet должны быть ==thread-safe==**

```
Клиент HTTP-запрос
        ↓
    [Connector] (парсит HTTP)
        ↓
[Engine → Host → Context] (матчинг)
        ↓
[Wrapper] (находит сервлет)
        ↓
 ┌─────────────────────┐
 │ FilterChain (вперед) │ ← Filter1.doFilter()
 │ Filter1.doFilter()  │ ← Filter2.doFilter()
 │ Filter2.doFilter()  │ ← FilterN.doFilter()
 │ ...                 │
 │ FilterN.doFilter()  │
 └─────────────────────┘
        ↓ chain.doFilter(req, resp)
 ┌─────────────────────┐
 │   Target Servlet    │ ← service() → doGet()/doPost()
 │ service()           │
 └─────────────────────┘
        ↓
 ┌─────────────────────┐
 │ FilterChain (назад) │ ← FilterN обработка после
 │ FilterN (post)      │ ← Filter2 обработка после  
 │ Filter2 (post)      │ ← Filter1 обработка после
 │ Filter1 (post)      │
 └─────────────────────┘
        ↓
[Connector] → HTTP-ответ → Клиент
```



### Регистрация и доступ (mapping)


- Регистрация возможна двумя основными способами: через `web.xml` (элементы `<servlet>` и `<servlet-mapping>`) или через аннотацию `@WebServlet(urlPatterns = "/path")` в коде. 
- Аннотация `@WebServlet` поддерживает дополнительные атрибуты: `name`, `description`, `initParams`, `loadOnStartup`, `asyncSupported` и массив `urlPatterns` для нескольких путей. 

### Жизненный цикл сервлета

![[Pasted image 20260130161828.png]]

- Жизненный цикл управляется контейнером и включает три ключевых этапа: 
	- инициализация, 
	- обработка запросов 
	- уничтожение сервлета. 
	
- При первом обращении (или при загрузке при старте, если задан `loadOnStartup`) контейнер создаёт экземпляр сервлета и один раз вызывает метод `init(ServletConfig config)` для инициализации ресурсов. 
- Для каждого входящего запроса контейнер создаёт объекты `ServletRequest`/`ServletResponse` (или `HttpServletRequest`/`HttpServletResponse` для HTTP) и вызывает `service(req, res)`, который в `HttpServlet` внутри маршрутизирует вызовы к `doGet`, `doPost`, `doPut` и др. 
- При остановке приложения или удалении сервлета контейнер один раз вызывает `destroy()`, где освобождаются ресурсы (соединения с БД, потоки и т.п.), после чего объект может быть собран сборщиком мусора. 

---

## 2. Основные элементы и классы сервлетов

### Ключевые интерфейсы и классы Servlet API

- **`Servlet`** — базовый интерфейс, задающий методы жизненного цикла: `init`, `service`, `destroy`, а также методы для получения `ServletConfig` и информации о сервлете.   
- **`GenericServlet`** — абстрактная реализация `Servlet`, не привязанная к конкретному протоколу; упрощает создание собственных протокольных сервлетов. 
- **`HttpServlet`** — абстрактный класс для HTTP‑сервлетов; реализует `service` так, чтобы делегировать обработку в методы `doGet`, `doPost`, `doPut`, `doDelete`, `doHead`, `doOptions`, `doTrace`. 

### Объекты запроса, ответа и окружения

- **`ServletRequest` / `ServletResponse`** — базовые интерфейсы для доступа к данным запроса (параметры, атрибуты, поток ввода) и формирования ответа (код статуса, заголовки, поток вывода).
- **`HttpServletRequest` / `HttpServletResponse`** — HTTP‑специализации, добавляющие методы для работы с HTTP‑методами, заголовками, параметрами формы, cookies, сессиями и статусами ответов. 
- **`ServletConfig`** — объект конфигурации конкретного сервлета, предоставляющий доступ к init‑параметрам и `ServletContext`. 
- **`ServletContext`** — общий контекст веб‑приложения, позволяющий разделять ресурсы и атрибуты между сервлетами в одном приложении. 

### Дополнительные элементы

- **Инициализационные параметры** (`init-param` в `web.xml` или `initParams` в `@WebServlet`) позволяют передавать конфигурацию сервлету без изменения кода. 
- **URL‑mapping и фильтры**: кроме сервлетов, в приложении используются фильтры (`Filter`), которые могут перехватывать и модифицировать запросы/ответы до и после сервлета, но сам принцип URL‑mapping аналогичен.


----
#### [[HttpServlet - jakarta.servlet - Flashcards|Link to flashcards]]



---
### References:

