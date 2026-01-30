
Theory for the cards: [[WebApplicationContext - Spring MVC]]

FILE TAGS: spring

Q: Зачем нужен WebMvcConfigurer в Spring Web MVC? Как он связан с WebApplicationContext?
A: WebMvcConfigurer - интерфейс, имеющий default методы для настройки web контекста.
	
В классической конфигурации Spring MVC:
	
- **Root ApplicationContext**
    
    - Общий для всего приложения.
    
    - Хранит «инфраструктурные» и бизнес‑бины: сервисы, репозитории, DataSource, транзакционный менеджер и т.п.
    
- **WebApplicationContext**
    
    - Наследует бины из root‑контекста и добавляет «веб‑слой»: контроллеры, ViewResolver, HandlerMapping, конвертеры и др.
        
    - Как правило, **по одному `WebApplicationContext` на каждый `DispatcherServlet`**.
        
    - Может переопределять бины из родительского контекста и добавлять свои.
	
	- `Filters`, перед регистрацией в `FilterChain` хранятся именно здесь
	
Обычно используем WebMvcConfigurer как класс конфигурации web контекста, аннотируя как @Configuration (и @EnableWebMvc).
	
```java
@Configuration  
@EnableWebMvc  
public class WebConfig implements WebMvcConfigurer {
```
<!--ID: 1769524371317-->

Q: Расскажи, что ты знаешь про `WebApplicationContext`.
A: `WebApplicationContext` — это интерфейс, который **расширяет `ApplicationContext` и «знает» про веб‑окружение**:
	
```java
public interface WebApplicationContext extends ApplicationContext {
    ServletContext getServletContext();
}
```
	
> [!tip]
> `ServletContext` - контекст сервлет-контейнера (Tomcat)
	
Ключевые особенности:
	
- Имеет доступ к `ServletContext`.
    
- Добавляет веб‑специфичные scope’ы:
    
    - `request` — один объект на HTTP‑запрос;
    
    - `session` — один объект на HTTP‑сессию;
       
    - `application` / `globalSession` (в кластерах).
       
- Используется Spring MVC‑инфраструктурой: `DispatcherServlet`, `HandlerMapping`, `HandlerAdapter`, `ViewResolver`, `HandlerExceptionResolver` и т.п. создаются именно в `WebApplicationContext`.
<!--ID: 1769615844233-->

