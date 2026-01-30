
Theory for the cards: [[@EnableWebMvc - Spring MVC]]

FILE TAGS: spring spring_mvc

Q: Что делает аннотация @EnableWebMvc?
A: **`@EnableWebMvc`** **импортирует полную MVC конфигурацию** Spring (`WebMvcConfigurationSupport`), **включая DispatcherServlet, HandlerMapping, ViewResolver** и другие компоненты. **В Spring Boot НЕ нужна** — автоконфигурация делает это автоматически.
	
**Что именно включает `@EnableWebMvc`**
	
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
<!--ID: 1769616026834-->
