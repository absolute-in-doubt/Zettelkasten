
20-10-2025 21:34

Status: #baby 

Tags: [[Spring]] [[Jakarta.servlet]]

---
# Filter - Spring

Фильтр - объект, который перехватывает все входящие запросы.

~={orange}`Filter` can be used to:=~

- Prevent downstream (следующие по [[Security Filter Chain - Spring Security#Порядок применения Security Filters|порядку в Filter Chain]]) `Filter` instances or the `Servlet` from being invoked. In this case, the `Filter` typically writes the `HttpServletResponse`.

-  Modify the `HttpServletRequest` or `HttpServletResponse` used by the downstream `Filter` instances and the `Servlet`.

```java
@Component
public class CustomFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) 
            throws java.io.IOException, jakarta.servlet.ServletException {
        // Логика до: логирование, проверка заголовков
        chain.doFilter(request, response);  // Передача дальше
        // Логика после: очистка
    }
}
```

---
## Use cases:

Перенаправление запросов на методы с HTTP методами PATCH, PUT, DELETE, т.к. [[формы - HTML#Атрибуты тега `<form>`|из HTML формы мы не можем отправить, что-то кроме POST или GET]]

класс замены web.xml с конфигурацией фильтра для поиска имени метода в спрятанном поле:
```java
public class MySpringMVCDispatcherServletInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {  
    @Override  
    protected Class<?>[] getRootConfigClasses() {  
        return null;  
    }  
  
    @Override  
    protected Class<?>[] getServletConfigClasses() {  
        return new Class[] {SpringConfig.class};  
    }  
  
    @Override  
    protected String[] getServletMappings() {  
        return new String[] {"/"};  
    }  
  
    @Override  
    public void onStartup(ServletContext aServletContext) throws ServletException {  
        super.onStartup(aServletContext);  
		  registerHiddenFieldFilter(aServletContext);
    }  
  
    private void registerHiddenFieldFilter(ServletContext aContext){  
        aContext.addFilter("hiddenHttpMethodFIlter", new HiddenHttpMethodFilter())  
                .addMappingForUrlPatterns(null, true, "/*");  
    }  
}
```


### В Spring Boot

Этот класс инициализации DispatcherServlet не нужен, его автоматически сконфигурирует Spring Boot. А default filters для добавления фильтров, их достаточно прописать в [[application.properties - Spring Boot#Добавление фильтра к Dispatcher servlet|application.properties]]

### [[Регистрация фильтров в FilterChain - jakarta.servlet|Регистрация custom фильтров]]






----
#### [[Filter - jakarta.servlet - Flashcards|Link to flashcards]]



---
### References:
