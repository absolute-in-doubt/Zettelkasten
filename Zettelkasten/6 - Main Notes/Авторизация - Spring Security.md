
12-02-2026 18:08

Status:

Tags: [[Spring Security]] [[Spring]]

---
# Авторизация - Spring Security

Для использования **~={purple}@PreAuthorize(`"hasRole('SOME_ROLE')"`)=~** необходимо **~={purple}@EnableMethodSecurity=~** в pure Spring.


### 1. Общая архитектура авторизации

- **Authentication** — объект с данными аутентифицированного пользователя: `principal` (UserDetails), `credentials` (пароль), `authorities` (роли/права).
    
- **SecurityContext** — хранит `Authentication` в `SecurityContextHolder` ([[ThreadLocal - Multithreading Java|ThreadLocal]]).
    
- **Authorization** — проверка прав **ПОСЛЕ** аутентификации: "может ли этот пользователь выполнить действие?".


### 2. Схема авторизации (последовательность)

```
1. Запрос → SecurityFilterChain (фильтры)
   ↓
2. Аутентификация (JwtFilter / UsernamePasswordFilter)
   ↓ AuthenticationManager.authenticate()
   ↓ Создаёт аутентифицированный Authentication
3. SecurityContextHolder.setAuthentication(auth)
   ↓
4. Authorization checks:
   • URL-level: HttpSecurity.antMatchers().hasRole()
   • Method-level: @PreAuthorize → MethodSecurityInterceptor
   • AOP-прокси проверяет expression (authentication.hasRole('ADMIN'))
   ↓
5. Метод выполняется / 403 Forbidden
```


---
### 3. Роль Authentication в авторизации

- Статус пользователя (роли, права) хранится в `auth.getAuthorities()` и `auth.getPrincipal()`.
    
- **Установка**: После `authenticate()` в фильтре/контроллере.
    
```java
Authentication auth = authenticationManager.authenticate(token);
SecurityContextHolder.getContext().setAuthentication(auth);
```
	
- **Доступ**: Везде через `SecurityContextHolder.getContext().getAuthentication()`.


---
### 4. @PreAuthorize — как работает

- **Включается**: `@EnableMethodSecurity(prePostEnabled = true)` → AOP-прокси.
    
- **Выполняется**: **ДО** метода (`pre`), оценивает SpEL-выражение:
    
```java
@PreAuthorize("hasRole('ADMIN')")
@PreAuthorize("hasAuthority('READ')")
@PreAuthorize("#user.id == authentication.principal.id")  // #param
```
	
- **Контекст**: `MethodSecurityExpressionRoot` с `authentication` (роли), `permissionEvaluator`, параметры метода.
    
- **Исключение**: `AccessDeniedException` → 403.


---
### 5. Типы проверок

```
• URL: .requestMatchers("/admin/**").hasRole("ADMIN")
• Method: @PreAuthorize, @Secured, @RolesAllowed
• Custom: CustomPermissionEvaluator
• Reactive: @PreAuthorize в WebFlux
```


---
### 6. Важные моменты

- **ProxyTargetClass = true** для CGLIB-прокси (private/final методы).
    
- **Self-injection**: `@PreAuthorize("@userService.hasAccess(authentication, #id)")`.
    
- **Performance**: Кэширование выражений в `MethodSecurityExpressionHandler`.
    


----
#### [[Авторизация - Spring Security - Flashcards|Link to flashcards]]



---
### References:

