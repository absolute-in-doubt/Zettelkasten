
Theory for the cards: [[Strategy - Pattern]]

FILE TAGS: java_interview patterns

Q: Что такое паттерн Strategy? Для чего он нужен? Работали ли с ним? Какие реализации знаете?
A:    
![[Pasted image 20260214113930.png]]
	
Выделяет логику в отдельную сущность, позволяя легко добавлять новые реализации.
	
---
**Пример**
	
В Spring Security AuthenticationManager (обычно ProviderManager) — это композитный менеджер, который держит список AuthenticationProvider'ов как стратегий. Каждый провайдер проверяет свой тип (метод supports()), и менеджер вызывает authenticate() только для подходящих, обеспечивая взаимозаменяемые алгоритмы аутентификации (DB, LDAP, OAuth).
	
Например:
	
- UsernamePasswordAuthenticationToken → DaoAuthenticationProvider (пароль из БД).
    
- JwtAuthenticationToken → JwtAuthenticationProvider (токен JWT).
    
- BasicAuthenticationToken → BasicAuthenticationProvider.
    
Это позволяет легко добавлять/менять провайдеры без изменения кода менеджера, следуя принципу Open-Closed.
<!--ID: 1771059388948-->

