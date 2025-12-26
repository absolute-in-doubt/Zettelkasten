
03-12-2025 12:12

Status:

Tags: [[Spring]]

---
# Security Filter Chain - Spring Security


#### Autoconfigured Security Filter Chain

(находится в SpringBootWebSecurityConfiguration классе)

```java
@Bean
@Order(SecurityProperties.BASIC_AUTH_ORDER)
SecurityFilterChain defaultSecurityFilterChain(HttpSecurity http) therows Exception {
	http.authorizationHttpRequest((requests) -> requests.anyRequest().authenticated); //purple filter
	http.formLogin(withDefaults()); //sets up 3 green filters
	http.httpBasic(withDefaults()); // pink filter
	return http.build();
}
```


### Default filter chain

Слева - метод, с помощью которого можно с конфигурировать фильтр.

(`WebAsyncManagerIntegrationFilter` can't be configured)

![[Pasted image 20251203122050.png]]

~={green}Зелёные фильтры=~ - добавляемые автоконфигурацией.



Defaut конфигурация ~={blue} синих фильтров=~ (это делается автоматически):

```java
@Bean
@Order(SecurityProperties.BASIC_AUTH_ORDER)
SecurityFilterChain defaultSecurityFilterChain(HttpSecurity http) therows Exception {
	
	return http.sessionManagement(withDefaults())
	.securityContext(withDefaults())
	.headers(withDefaults())
	.cors(withDefaults())
	.logout(withDefaults())
	.requestCache(withDefaults())
	.servletApi(withDefaults())
	.anonymous(withDefaults())
	.exceptionHandling(withDefaults())
	.build();
}
```


---
### Порядок применения Security Filters

Определяется в классе `FilterOrderGeneration`

The order is predefined:

![[Pasted image 20251203123249.png]]
....


~={red}!!!=~ ~={orange}Это класс конфигурирует не только Security FIlters. Он устанавливает порядок применения всех фильтров=~ ~={red}!!!=~

![[Pasted image 20251203123419.png]]
![[Pasted image 20251203123600.png]]

~={orange}The lower order - the higher priority=~


----
#### [[Security Filter Chain - Spring Security - Flashcards|Link to flashcards]]



---
### References:

