
18-10-2025 19:14

Status: #baby 

Tags: [[Spring]]

---
# DispatcherServlet - Spring MVC

![[Pasted image 20251018191435.png]]

`*`"реализован командой Spring" - имется ввиду командой разработчиков Spring

### HandlerMapper

- Находит **обработчик (handler)** для входящего HTTP‑запроса: контроллер или другой обработчик.
    
- Сопоставляет URL, HTTP‑метод, параметры и т.п. с методом контроллера.

Для этого использует реализацию HandlerMapping. (default implementation - BeanNameUrlHandlerMapping and RequestMappingHandlerMapping)

`RequestMappingHandlerMapping` — стандартный маппер для аннотированных контроллеров (`@Controller`, `@RequestMapping`, `@GetMapping` и т.п.).

##### **Как работает в цепочке**

- `DispatcherServlet` получает запрос и спрашивает все зарегистрированные `HandlerMapping` по очереди: «Кто обработает этот запрос?».
    
- Первый, кто возвращает подходящий обработчик, «побеждает», и его `HandlerExecutionChain` (handler + интерцепторы) используется далее.



---
### HandlerAdapter

**За что отвечает**

- Преобразует **обработчик** в фактический **вызов** (инвокацию) правильного метода, с учётом его типа.
    
- Позволяет `DispatcherServlet` работать с разными типами handler’ов (аннотированные контроллеры, старые `Controller`, `HttpRequestHandler` и др.), не зная их деталей.
    

**Как работает в цепочке**

- После того как `HandlerMapping` вернул handler, `DispatcherServlet` спрашивает все `HandlerAdapter`:  
    «Кто умеет обслуживать этот тип handler’а?».
    
- Найденный адаптер:
    
    - подготавливает аргументы (request, response, модель, path‑параметры, `@RequestBody`, `@PathVariable` и т.п.);
        
    - вызывает нужный метод контроллера;
        
    - получает результат (например, `ModelAndView` или `ResponseEntity`) для дальнейшего рендеринга.
        

**Примеры реализаций**

- `RequestMappingHandlerAdapter` — адаптер для современных аннотированных контроллеров (`@Controller`, `@RestController`).
    
- `HttpRequestHandlerAdapter`, `SimpleControllerHandlerAdapter` — адаптеры для других типов обработчиков.


---
## HandlerExceptionResolver

**За что отвечает**

- Централизованно обрабатывает исключения, возникшие при обработке запроса.

- Преобразует исключения в HTTP‑ответы: страницу ошибки, JSON‑ответ, redirect и т.п.


**Как работает в цепочке**

- Если во время вызова handler’а или рендеринга view возникает исключение, `DispatcherServlet` передаёт его цепочке `HandlerExceptionResolver`.
    
- Каждый `HandlerExceptionResolver` по очереди пытается:
    
    - распознать тип исключения;
    
    - вернуть `ModelAndView` или «short‑circuit»‑ответ (например, HTTP‑статус).
    
- Как только один резолвер успешно обработает исключение (вернёт не‑null результат), остальные уже не вызываются.


**Примеры реализаций**

- `ExceptionHandlerExceptionResolver` — поддержка [[@ExceptionHandler - Spring Web MVC|@ExceptionHandler]] в контроллерах.
    
- `ResponseStatusExceptionResolver` — обрабатывает [[@ResponseStatus - Spring Web MVC|@ResponseStatus]] и `ResponseStatusException`.
    
- `DefaultHandlerExceptionResolver` — маппит стандартные исключения Spring MVC/Servlet API на подходящие HTTP‑коды (400, 404, 405, 415, 500 и т.д.).
    

Таким образом, **HandlerExceptionResolver отвечает за то, «как превратить выброшенное исключение в корректный HTTP‑ответ/страницу ошибки»**.

----
#### [[DispatcherServlet - Spring MVC - Flashcards|Link to flashcards]]



---
### References:

