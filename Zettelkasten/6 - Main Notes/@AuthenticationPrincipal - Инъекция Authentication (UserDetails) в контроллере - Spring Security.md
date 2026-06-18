
16-02-2026 18:38

Status:

Tags: [[Spring Security]] [[Spring]]

---
# Инъекция Authentication (UserDetails) в контроллере - Spring Security

В Spring Security объект `Authentication` можно инжектить прямо в параметры метода контроллера — фреймворк автоматически его разрешит из `SecurityContextHolder`. Это самый простой и идиоматичный способ получить текущего пользователя, роли и другие данные из токена/JWT.

## Прямой инжект Authentication

```java
@RestController
public class UserController {
    
    @GetMapping("/profile")
    public ResponseEntity<String> getProfile(Authentication authentication) {
        String username = authentication.getName();  // username из principal
        UserDetails userDetails = (UserDetails) authentication.getPrincipal();
        Collection<? extends GrantedAuthority> roles = userDetails.getAuthorities();
        
        return ResponseEntity.ok("User: " + username + ", roles: " + roles);
    }
}
```


    
- `authentication.isAuthenticated()` проверяет статус
    
- **~={red}Доступен только в защищенных методах (после фильтров Security)=~**[​


---
### Инжект текущего пользователя (@AuthenticationPrincipal)

Более типизированный способ для кастомного `UserDetails`:

```java
@GetMapping("/profile")
public String getProfile(@AuthenticationPrincipal CustomUserDetails user) {
    return "Hello, " + user.getUsername() + " (" + user.getEmail() + ")";
}
```

В `CustomUserDetails` добавьте нужные поля (email, ID, роли). Spring Security автоматически извлечет principal и приведет к вашему типу.​

> [!warning] 
> `@AuthenticationPrincipal` resolves to `Authentication.getPrincipal()` — not the `Authentication` object itself.
> 
>~={red} **Works with any type:** не обязательно, чтобы он расширял `UserDetails`=~


---
### SecurityContextHolder (ручной доступ)

Если не хотите в параметры:

```java
@GetMapping("/profile")
public String getProfile(HttpServletRequest request) {
    Authentication auth = SecurityContextHolder.getContext().getAuthentication();
    return auth.getName();
}
```

Менее удобен, но работает везде (сервисах, утилитах)​


---
## Кастомный аргумент резолвер (редко нужен)

Для сложной логики создайте `HandlerMethodArgumentResolver`:

```java
@Component
public class CurrentUserResolver implements HandlerMethodArgumentResolver {
    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        return parameter.hasParameterAnnotation(CurrentUser.class);
    }
    
    @Override
    public Object resolveArgument(MethodParameter parameter, 
                                ModelAndViewContainer mavContainer, 
                                NativeWebRequest webRequest, 
                                WebDataBinderFactory binderFactory) {
        Authentication auth = SecurityContextHolder.getContext().getAuthentication();
        return ((CustomUserDetails) auth.getPrincipal()).getUserEntity();
    }
}
```

Контроллер: `public String profile(@CurrentUser User user)`
​



----
#### [[Инъекция Authentication (UserDetails) в контроллере - Spring Security - Flashcards|Link to flashcards]]



---
### References:

