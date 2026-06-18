
14-02-2026 11:28

Status:

Tags: [[Patterns]]

---
# Strategy - Pattern


![[Pasted image 20260214113930.png]]


Выделяет логику в отдельную сущность, позволяя легко добавлять новые реализации.




### Пример


## Практический пример в Spring Security

В Spring Security AuthenticationManager (обычно ProviderManager) — это композитный менеджер, который держит список AuthenticationProvider'ов как стратегий. Каждый провайдер проверяет свой тип (метод supports()), и менеджер вызывает authenticate() только для подходящих, обеспечивая взаимозаменяемые алгоритмы аутентификации (DB, LDAP, OAuth).

Например:

- UsernamePasswordAuthenticationToken → DaoAuthenticationProvider (пароль из БД).
    
- JwtAuthenticationToken → JwtAuthenticationProvider (токен JWT).
    
- BasicAuthenticationToken → BasicAuthenticationProvider.
    

Это позволяет легко добавлять/менять провайдеры без изменения кода менеджера, следуя принципу [[SOLID|Open-Closed]].


----
#### [[Strategy - Pattern - Flashcards|Link to flashcards]]



---
### References:

- [[AuthenticationManager - Spring Security]]
- [[AuthenticationProvider - Spring Security]]