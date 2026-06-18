
03-12-2025 12:49

Status:

Tags: [[Spring]]

---
# Adding Custom Security FIlter - Spring Security


У HttpSecurity есть методы:

- `#addFilterBefore(Filter, Class<?>)` adds your filter before another filter
    
- `#addFilterAfter(Filter, Class<?>)` adds your filter after another filter
    
- `#addFilterAt(Filter, Class<?>)` replaces another filter with your filter

> [!tip]
> Most commonly, applications add a custom authentication. This means they should be placed after [`LogoutFilter`](https://docs.spring.io/spring-security/reference/servlet/authentication/logout.html).


#### Key events in the filter chain:

1. [`SecurityContext`](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-securitycontextholder) is loaded from the session
    
2. Request is protected from common exploits; [secure headers](https://docs.spring.io/spring-security/reference/features/exploits/headers.html), [CORS](https://docs.spring.io/spring-security/reference/servlet/integrations/cors.html), [CSRF](https://docs.spring.io/spring-security/reference/servlet/exploits/csrf.html)
    
3. Request is [authenticated](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html)
    
4. Request is [authorized](https://docs.spring.io/spring-security/reference/servlet/authorization/architecture.html)


| If your filter is a(n)    | Then place it after           | As key events have already occurred |
| ------------------------- | ----------------------------- | ----------------------------------- |
| exploit protection filter | SecurityContextHolderFilter   | 1                                   |
| authentication filter     | LogoutFilter                  | 1, 2                                |
| authorization filter      | AnonymousAuthenticationFilter | 1, 2, 3                             |

### Пример добавления фильтра:

```java
@Bean
SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        // ...
        .addFilterAfter(new TenantFilter(), AnonymousAuthenticationFilter.class);
    return http.build();
}
```

Добавляем после AnonymousAuthenticationFilter, т.к. наш фильтр авторизирут юзера как постояльца (tenant).


---
### Проверка подключённых фильтров

При запуске приложения можно вывести на экран список применяемых фильтров.

Для этого запускаем приложение на уровне DEBUG.

Будет подобный output:

```
2023-06-14T08:55:22.321-03:00  DEBUG 76975 --- [main] o.s.s.web.DefaultSecurityFilterChain     : Will secure any request with [ 
	DisableEncodeUrlFilter, 
	WebAsyncManagerIntegrationFilter, 
	SecurityContextHolderFilter, 
	HeaderWriterFilter, 
	CsrfFilter, 
	LogoutFilter, 
	UsernamePasswordAuthenticationFilter, 
	DefaultLoginPageGeneratingFilter, 
	DefaultLogoutPageGeneratingFilter, 
	BasicAuthenticationFilter, 
	RequestCacheAwareFilter, 
	SecurityContextHolderAwareRequestFilter, 
	AnonymousAuthenticationFilter, 
	ExceptionTranslationFilter, 
	AuthorizationFilter]
```

(только там будет в строчку, тут просто это выглядило бы уёбищно)

----
#### [[Custom Security FIlter - Spring Security - Flashcards|Link to flashcards]]



---
### References:

