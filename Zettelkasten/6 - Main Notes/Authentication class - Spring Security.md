
12-02-2026 19:42

Status:

Tags: [[Spring]]

---
# Authentication class - Spring Security

*~={pink}Супер важный класс. Вокруг него построена вся лоогика Spring Security. Инфы дохуя. Пока главное не выделил.=~*



### 1. Структура класса Authentication

**Интерфейс** с **5 основными полями**:

```java
public interface Authentication extends Principal {
    Object getPrincipal();           // UserDetails / username
    Object getCredentials();         // password / token
    Collection<? extends GrantedAuthority> getAuthorities(); // ROLE_USER, ROLE_ADMIN
    boolean isAuthenticated();       // authenticated = true/false
    Object getDetails();             // WebAuthenticationDetails (IP, sessionId)
}
```


---
### 2. Unauthenticated vs Authenticated

| Состояние           | `isAuthenticated()` | `principal`         | `credentials`              | `authorities`   |
| ------------------- | ------------------- | ------------------- | -------------------------- | --------------- |
| **Unauthenticated** | `false`             | `String` (username) | `String` (raw password)    | `null`          |
| **Authenticated**   | `true`              | `UserDetails`       | `encodedPassword` / `null` | `["ROLE_USER"]` |

~={red}**Ключ**: `isAuthenticated()` **НЕ** меняется вручную!=~ Устанавливается **автоматически** в `AbstractAuthenticationToken`.


---
### 3. Схема заполнения полей

```
1. Filter создаёт UNAUTHENTICATED:
UsernamePasswordAuthenticationToken("user", "pass", null)
    ↓ principal="user", credentials="pass", authenticated=false

2. AuthenticationProvider.authenticate():
   ↓ UserDetailsService.loadUserByUsername()
   ↓ Создаёт AUTHENTICATED:
return new UsernamePasswordAuthenticationToken(userDetails, null, authorities)
    ↓ principal=UserDetails, credentials=null, authenticated=true

```


---
### 4. Какие классы используют Authentication и что смотрят

| Компонент                            | Поле               | Что проверяет                              |
| ------------------------------------ | ------------------ | ------------------------------------------ |
| **@PreAuthorize**                    | `getAuthorities()` | `hasRole('ADMIN')` → `ROLE_ADMIN` в списке |
| **HttpSecurity**                     | `getAuthorities()` | `.hasRole("ADMIN")` для URL                |
| **JwtAuthenticationConverter**       | `getPrincipal()`   | `Jwt` → извлекает `sub`, `scope`           |
| **SecurityContextPersistenceFilter** | `getDetails()`     | Сохраняет в сессию                         |
| **RememberMeServices**               | `getPrincipal()`   | Создает remember-me токен                  |


---
### 5. Конкретные реализации

```java
// 1. UNAUTHENTICATED (Filter)
UsernamePasswordAuthenticationToken unauth = 
    new UsernamePasswordAuthenticationToken("user", "pass");

// 2. AUTHENTICATED (Provider)
UsernamePasswordAuthenticationToken auth = 
    new UsernamePasswordAuthenticationToken(userDetails, null, authorities);

// 3. JWT
JwtAuthenticationToken jwtAuth = 
    new JwtAuthenticationToken(jwt, authorities);
    ↓ principal=Jwt, authorities=["ROLE_ADMIN"]
```

## 6. Полный жизненный цикл

```
1. Filter: new UsernamePasswordAuthenticationToken(username, rawPassword)
   ↓ authenticated=false, principal=username
   
2. ProviderManager.authenticate():
   ↓ DaoAuthenticationProvider
   ↓ UserDetailsService → UserDetails
   ↓ PasswordEncoder.matches()
   ↓ return new UsernamePasswordAuthenticationToken(UserDetails, null, authorities)
      ↓ authenticated=true, principal=UserDetails
   
3. SecurityContextHolder.setAuthentication(auth)
   
4. @PreAuthorize("hasRole('ADMIN')") → auth.getAuthorities().contains(ROLE_ADMIN)
```

## 7. Практический пример

[[AuthenticationManager - Spring Security|Инфа про AuthenticationManager]]

```java
// ДО аутентификации
Authentication unauth = SecurityContextHolder.getContext().getAuthentication();
unauth.isAuthenticated() == false  // principal=username

// ПОСЛЕ (AuthenticatioNManager - фасад для реализций AuthenticationProvider)
Authentication auth = authenticationManager.authenticate(unauth);
auth.isAuthenticated() == true     // principal=UserDetails
auth.getAuthorities()              // [ROLE_USER]
```

**Ключевые поля для авторизации**: `getAuthorities()` (роли) и `getPrincipal()` (UserDetails/JWT).


----
#### [[Authentication class - Spring Security - Flashcards|Link to flashcards]]



---
### References:

