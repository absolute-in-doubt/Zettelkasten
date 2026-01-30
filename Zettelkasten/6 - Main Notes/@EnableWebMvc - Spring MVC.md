
28-01-2026 18:58

Status:

Tags: [[Spring]]

---
# @EnableWebMvc - Spring MVC

**`@EnableWebMvc`** **импортирует полную MVC конфигурацию** Spring (`WebMvcConfigurationSupport`), **включая DispatcherServlet, HandlerMapping, ViewResolver** и другие компоненты. **В Spring Boot НЕ нужна** — автоконфигурация делает это автоматически.

## Что **именно** включает `@EnableWebMvc`

```
✅ DispatcherServlet автоматическая настройка
✅ RequestMappingHandlerMapping (@GetMapping/@PostMapping)
✅ RequestMappingHandlerAdapter 
✅ ExceptionHandlerExceptionResolver (@ExceptionHandler)
✅ ViewResolver (JSP, Thymeleaf)
✅ ContentNegotiationConfigurer (JSON/XML)
✅ DefaultServletHandlerMapping (статические файлы)
✅ Форматировщики дат (@DateTimeFormat)
✅ Валидация (@Valid)
```


----
#### [[@EnableWebMvc - Spring MVC - Flashcards|Link to flashcards]]



---
### References:

