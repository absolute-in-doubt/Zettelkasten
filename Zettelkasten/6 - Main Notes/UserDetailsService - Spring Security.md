
02-12-2025 21:02

Status:

Tags: [[Spring Security]] [[Spring]]

---
# UserDetailsService - Spring Security

Его реализация автоматически подтягивается Spring Security для валидации и аутентификации пользователей.

Пример реализации (тут только один метод, но вообще их много):

```java
@Service
public class PersonDetaisService implements UserDetailsService {

	private final PeopleRepository repository;
	
	@Autowired
	public PersonDetailsService(PeopleRepository peopleRepository){
	this.repository = peopleRepository;
	}
	
	@Override
	public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
		Optional<Person> person = repository.findByUsername(s);
		
		if(person.isEmpty())
			throw new UserNotFoundException("User not found");
		
		return person.get();
	}
}
```


Spring Security отловит UserNotFound и покажет его содержимое на странице логина.

~={orange}Методы реализации UserDetailsService  возвращают реализации UserDetails:=~

##### Класс Spring Security для хранения данных пользователя

```java
public class PersonDetails implements UserDetails {
	private final Person person;
	
	public Collection<? extends GrantedAuthority> getAuthorities() {
		return null;
	}
	
	@Override
	public String getPassword() {
		return person.getPassword();
	}
	
	@Override
	public String getUsername() {
		return person.getUsername();
	}
	
	@Override
	public boolean isAccontExpired() {
		return false;
	}
	
	...
	
}
```

----
#### [[UserDetailsService - Spring Security - Flashcards|Link to flashcards]]



---
### References:

- [[AuthenticationProvider - Spring Security]]