
03-12-2025 13:02

Status:

Tags:

---
# Custom Security Filter - Spring Security

Все фильтры реализуют `Filter`, который определяет наличие метода doFilter(...)

```java
public class TenantFilter implements Filter {

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        HttpServletResponse response = (HttpServletResponse) servletResponse;

        String tenantId = request.getHeader("X-Tenant-Id");
        boolean hasAccess = isUserAllowed(tenantId); 
        if (hasAccess) {
            filterChain.doFilter(request, response); //вызов остальных фильтров из FilterChain (передаём запрос дальше) 
            return;
        }
        throw new AccessDeniedException("Access denied"); // запрос дальше не передаётся
    }

}
```

> [!tip]
> Instead of implementing `Filter`, you can extend from [OncePerRequestFilter](https://docs.spring.io/spring-framework/docs/7.0.0/javadoc-api/org/springframework/web/filter/OncePerRequestFilter.html) which is a base class for filters that are only~={orange} invoked once per request =~and provides a `doFilterInternal` method with the `HttpServletRequest` and `HttpServletResponse` parameters.


#### [[Adding Custom Security FIlter - Spring Security]]

----
#### [[Custom Security Filter - Spring Security - Flashcards|Link to flashcards]]



---
### References:

