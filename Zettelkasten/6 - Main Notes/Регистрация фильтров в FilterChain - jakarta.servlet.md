
08-01-2026 12:55

Status:

Tags: [[Jakarta.servlet]] [[Java+]]

---
# Регистрация фильтров в FilterChain - Jakarta.servlet


### 1. Через web.xml

```xml
<web-app ...>

    <filter>
        <filter-name>loggingFilter</filter-name>
        <filter-class>com.example.LoggingFilter</filter-class>
    </filter>

    <filter-mapping>
        <filter-name>loggingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

</web-app>
```


### 2. Через аннотацию WebFilter

```java
import jakarta.servlet.*;
import jakarta.servlet.annotation.WebFilter;
import java.io.IOException;

@WebFilter("/*")
public class LoggingFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request,
                         ServletResponse response,
                         FilterChain chain)
            throws IOException, ServletException {

        System.out.println("Before servlet");
        chain.doFilter(request, response);
        System.out.println("After servlet");
    }
}
```


### 3. Программная регистрация через `ServletContext`+`FilterRegistration`

`ServletContextListener` - класс для доп настроек загрузки сервлет контекста в Tomcat/Jetty

```java
import jakarta.servlet.*;

public class MyAppInitializer implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext ctx = sce.getServletContext();

        FilterRegistration.Dynamic reg =
                ctx.addFilter("loggingFilter", new LoggingFilter());

        reg.addMappingForUrlPatterns(
                null,         // dispatcher types (null = REQUEST по умолчанию в некоторых контейнерах)
                false,        // isMatchAfter: добавлять до уже объявленных (false) или после (true)
                "/*"
        );
    }
}

```

----
#### [[Регистрация фильтров в FilterChain - Jakarta.servlet - Flashcards|Link to flashcards]]



---
### References:

