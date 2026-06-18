
02-12-2025 20:52

Status:

Tags: [[Spring]]

---
# config - Spring Security


### Конфигурационный класс

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	protected void configure(AuthenticationManagerBuilder auth){
		auth.authenticationProvider();
	}

}
```

**~={purple}@EnableWebSecurity=~**  - нужна только с чистым Spring. 


#### AuthProvider:


----
#### [[config - Spring Security - Flashcards|Link to flashcards]]



---
### References:

