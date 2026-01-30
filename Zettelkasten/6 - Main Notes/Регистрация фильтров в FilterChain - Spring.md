
08-01-2026 13:05

Status:

Tags: [[Spring]] [[Jakarta.servlet]] [[Java+]]

---
# Регистрация фильтров в FilterChain - Spring


## В чистом Spring

#### 1. Регистрируем так же как и [[Регистрация фильтров в FilterChain - jakarta.servlet|в Jakarta EE]].


#### 2. Так же как в [[Регистрация фильтров в FilterChain - jakarta.servlet|в Jakarta EE]], но с загрузкой фильтров в ApplicationContext

Пример регистрации бина SpringSecurityFilterChain ([[FilterChain - jakarta.servlet#SecurityFilterChain в контексте FilterChain|регистрируется как обычный bean в FilterChain]]):

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth.anyRequest().authenticated())
            .formLogin(withDefaults());
        return http.build();   // создаёт SecurityFilterChain
    }
}
```

> [!warning] 
> Filter beans относятся к web контексту (`WebApplicationContext`)


Регистрация в web.xml:

```xml
<filter>
    <filter-name>springSecurityFilterChain</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
</filter>

<filter-mapping>
    <filter-name>springSecurityFilterChain</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

> [!tip] Note
> Используем `DelegatingFilterProxy` а не путь к конкретному классу. Spring найдет нужный bean в контексте по имени.


#### 3. В классе конфигурации DispatcherServlet

```java
public class MyWebAppInitializer
        extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[]{ RootConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[]{ WebConfig.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{ "/" };
    }

    @Override
    protected Filter[] getServletFilters() {
        return new Filter[]{
            new HiddenHttpMethodFilter(),
            new CharacterEncodingFilter()
        };
    }
}
```



---
## В Spring Boot

#### 1. Кастомный фильтр

Достаточно указать @Component над своей реализацией фильтра - зарегать как bean. 

```java
@Component
public class LoggingFilter implements Filter {
...
}
```

В дополнение к этому надо на его основе создать FilterRegistrationBean:

```java
@Configuration
public class WebConfig {

    @Bean
    public FilterRegistrationBean<LoggingFilter> loggingFilterReg(LoggingFilter filter) {
        FilterRegistrationBean<LoggingFilter> reg = new FilterRegistrationBean<>(filter);
        reg.addUrlPatterns("/*");
        reg.setOrder(1);
        return reg;
    }
}
```






---
### References:

