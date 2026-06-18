
14-06-2026 10:54

Status:

Tags: [[Spring Security]] [[Spring]]

---
# Автоконфигурация UserDetailsService - Spring Security


Spring Security уже предоставляет несколько готовых реализаций:

#### InMemoryUserDetailsManager

Для тестов и демо.

```java
new InMemoryUserDetailsManager(...)
```


---
#### JdbcUserDetailsManager

Для хранения пользователей в БД.

```java
new JdbcUserDetailsManager(dataSource)
```

> [!warning]
> Таблицы в БД он НЕ создает автоматически

Spring Security просто ожидает, что в БД уже есть таблицы определенной структуры.

По умолчанию используются две таблицы:

1. users — пользователи
    
2. authorities — роли/права пользователя

Default schema: ([Spring docs on that]([JDBC Authentication :: Spring Security](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/jdbc.html)))

```sql
create table users(
	username varchar_ignorecase(50) not null primary key,
	password varchar_ignorecase(500) not null,
	enabled boolean not null
);

create table authorities (
	username varchar_ignorecase(50) not null,
	authority varchar_ignorecase(50) not null,
	constraint fk_authorities_users foreign key(username) references users(username)
);
create unique index ix_auth_username on authorities (username,authority);
```


### Можно ли использовать свои таблицы и колонки?

Да. `JdbcUserDetailsManager` позволяет переопределить SQL-запросы:

Тогда структура БД может быть любой, главное — чтобы запросы возвращали данные в нужном порядке:

1. users query → `username`, `password`, `enabled`
    
2. authorities query → `username`, `authority`
    

### Что насчет ролей и префикса ROLE_

Когда ты создаешь пользователя так:

Spring автоматически сохранит authority как:

Если используешь `.authorities("ADMIN")`, то сохранится ровно `ADMIN` без префикса.


---
### Простая ручная конфигурация

```java
@Bean
public UserDetailsService userDetailsService(UserRepository repository) {
    return username ->
            repository.findByUsername(username)
                    .map(CustomUserDetails::new)
                    .orElseThrow(...);
}
```


```java
@Bean 
public UserDetailsService userDetailsService() { 
	UserDetails userDetails = User.withDefaultPasswordEncoder() 
			.username("user") 
			.password("password") 
			.roles("USER") 
			.build(); 
			
	return new InMemoryUserDetailsManager(userDetails); 
}
```





----
#### [[автоконфигурация UserDetailsService - Spring Security - Flashcards|Link to flashcards]]



---
### References:

