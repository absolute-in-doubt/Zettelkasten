
08-01-2026 12:43

Status:

Tags: [[Jakarta.servlet]] [[Java+]]

---
# FilterChain - jakarta.servlet

Контейнер (Tomcat/Jetty/Undertow) создаёт свою внутреннюю цепочку фильтров на основе регистраций в `ServletContext` и при каждом запросе вызывает:

- `filter1.doFilter(..., chain)` → внутри `chain.doFilter(...)` вызывается `filter2` и т.д., в конце — `Servlet` (в Spring это `DispatcherServlet`).​

Spring **не заменяет** `FilterChain`, а регистрирует свои фильтры (и твои кастомные) в контейнере, поэтому в `doFilter` всегда получаешь именно сервлетный `FilterChain`.




## Когда создаётся цепочка фильтров и где она хранится

- При старте приложения Spring Boot создаёт `ServletWebServerApplicationContext`, поднимает встроенный Tomcat/Jetty/Undertow и в процессе инициализации:
    
    - регистрирует `DispatcherServlet`,
        
    - находит все `Filter`‑бины и/или `FilterRegistrationBean`‑бины и регистрирует их в `ServletContext`​
        
- На стороне сервлет‑контейнера из всех зарегистрированных фильтров и их URL‑паттернов формируется внутренняя структура цепочек (обычно список/массив), на основе которой создаются `FilterChain`‑объекты для каждого запроса.


### SecurityFilterChain в контексте FilterChain

> [!tip] **SecurityFilterChain**
> Spring Security регистрирует свой Filter в FilterChain - `springSecurityFilterChain`- по сути хранится как один фильтр и через `DelegatingFilterProxy` вызывает свой внутренний SecurityFilterChain
> 
> Схематично цепочка может выглядеть так:
>
>1. `CharacterEncodingFilter` ← фильтр Spring Boot
> 
>2. `HiddenHttpMethodFilter` ← фильтр Spring Boot
   >
>3. `springSecurityFilterChain` ← фильтр Spring Security
>
>4. `RequestContextFilter`
>
>5. `DispatcherServlet`



---
### References:

