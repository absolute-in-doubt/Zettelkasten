
05-01-2026 10:08

Status:

Tags: [[Jakarta.servlet]] [[Java+]]

---
# Filter - jakarta.servlet

- Servlet‑фильтр — это компонент, который перехватывает запросы и/или ответы на пути между клиентом и сервлетом: можно выполнять аутентификацию, логирование, сжатие, добавление заголовков и т.д.​
    
- Основной метод интерфейса `jakarta.servlet.Filter` — `doFilter(ServletRequest req, ServletResponse res, FilterChain chain)`, внутри которого выполняется пред‑обработка, затем вызывается `chain.doFilter(...)` для передачи управления дальше и, при необходимости, пост‑обработка после возврата.


### Пример фильтра

```java
package com.example.filter;

import jakarta.servlet.Filter;
import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.ServletRequest;
import jakarta.servlet.ServletResponse;
import jakarta.servlet.annotation.WebFilter;
import jakarta.servlet.annotation.WebInitParam;
import java.io.IOException;

@WebFilter(
    filterName = "LoggingFilter",
    urlPatterns = "/*",
    initParams = {
        @WebInitParam(name = "enabled", value = "true")
    }
)
public class LoggingFilter implements Filter {

    private boolean enabled;

    @Override
    public void init(jakarta.servlet.FilterConfig filterConfig) {
        String param = filterConfig.getInitParameter("enabled");
        enabled = param == null || Boolean.parseBoolean(param);
    }

    @Override
    public void doFilter(
            ServletRequest request,
            ServletResponse response,
            FilterChain chain
    ) throws IOException, ServletException {

        if (!enabled) {
            // Просто передаём дальше без логирования
            chain.doFilter(request, response);
            return;
        }

        long start = System.currentTimeMillis();

        // Предобработка (до сервлета)
        System.out.println("Incoming request from IP: " + request.getRemoteAddr());

        // Передаём запрос следующему фильтру или целевому сервлету
        chain.doFilter(request, response);

        // Постобработка (после сервлета)
        long duration = System.currentTimeMillis() - start;
        System.out.println("Request processed in " + duration + " ms");
    }

    @Override
    public void destroy() {
        // Освобождение ресурсов при остановке приложения (если нужно)
    }
}

```

----
#### [[Filter - jakarta.servlet - Flashcards|Link to flashcards]]



---
### References:

