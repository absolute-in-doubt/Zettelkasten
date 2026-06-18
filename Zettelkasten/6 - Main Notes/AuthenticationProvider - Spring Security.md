
02-12-2025 20:22

Status:

Tags: [[Spring Security]] [[Spring]]
 
---
# AuthenticationProvider - Spring Security


![[Pasted image 20251202202257.png]]

Возвращается такой же объект, но содержащий Principal (может содержать что угодно, но обычно туда кладут найденного пользователя)

~={green}Объект Authentication помещается в сессию пользователя. За то, чтобы при каждом запросе этот объект подгружался из сессии отвечает отдельный фильтр Spring Security.=~

Теперь каждый раз, когда пользователь будет делать запрос к нашему приложению, мы будем иметь доступ к его Authentication объекту.


---

```java
@Component
public class AuthProviderImpl implements AuthenticationProvider {
	
	private final PersonDetailsService personDetailsService;
	
	public AuthProviderImpl(PersonDetailsService personDetailsService){
		this.personDetailsService = personDetailsService;
	}
	
	@Override 
	public Authentication authenticate(Authentication authentication) throws AuthenticationException {
		String username = authentication.getName();
		
		//checking whether user is valid and paswword is right
		UserDetails personDetails = personDetailsService.loadByUsername(username);
		
		String password = authentication.getCredentials().toString();
		
		if (!password.equals(personDetails.getPassword()))
			throw new BadCredentialsException("Incorrect password!");
		
		
		return new UsernamePasswordAuthenticationToken(personDetails, password, Collection.emptyList());
	} 
	
	
	@Override
	public boolean supports(Class<?> aClass){
		return true;
	}
}
```

нужен для проверки, подходит ли этот AuthenticationProvider для данной аутентификации

Т.к. в приложении может быть несколько AuthenticationProvider-ов.

> [!note]
> `BadCredentialsException("Incorrect password!")` - будет отловлен Spring Security и сообшение показано пользователю на странице логина


Обычно для поиска пользователя в БД и его валидации реализуют интерфейс [[UserDetailsService - Spring Security|Spring Security "UserDetailsService"]]

----
#### [[AuthenticationProvider - Spring Security - Flashcards|Link to flashcards]]



---
### References:

- [[UserDetailsService - Spring Security]]