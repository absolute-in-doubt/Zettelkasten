
17-03-2026 13:50

Status:

Tags: [[Spring Web MVC]] [[Spring]]

---
## @Controller & @Repository vs @Component 


~={purple}**@Controller**=~
	
Отвечает за обработку HTTP-запросов в веб-слое (Spring MVC).
	
- Распознаётся `DispatcherServlet` для маппинга методов с `@RequestMapping`.
    
- Позволяет использовать `@ResponseBody`, `@RestController` (комбинация с `@Controller` + `@ResponseBody`).
    
- Семантически указывает на контроллер-презентер, улучшая читаемость и инструменты IDE.
    
---
~={purple}**@Repository**=~
	
Используется для DAO/репозиториев в персистентном слое.
	
- Автоматически переводит исключения платформы (JDBC, JPA) в Spring-исключения (`DataAccessException`).
    
- Добавляет AOP-аспект для обработки ошибок БД без boilerplate-кода.
    
- Поддерживает транзакции и интеграцию с `JdbcTemplate`, JPA
	
| Аннотация     | Слой        | Доп. функциональность    |
| ------------- | ----------- | ------------------------ |
| `@Component`  | Любой       | Базовая: авто-детект, DI |
| `@Controller` | Web         | Маппинг запросов         |
| `@Repository` | Persistence | Обработка исключений БД  |





----
#### [[@Controller & @Repository vs @Component - Spring Web MVC - Flashcards|Link to flashcards]]



---
### References:

