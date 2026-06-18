
Theory for the cards: [[@Repository - Spring Data]]

FILE TAGS: spring

Q: В чём отличия аннотаций @Component, @Service, @Repository?
A: @Service - маркерная аннотация, делает то же самое, что и @Component (регистрирует в контексте)
	
**@Repository** отличается от **@Component** тем, что помиом регистрации в контексте, оборачивает выкидываемые `SQLException` в unchecked `DataAccessException`.
<!--ID: 1769527450047-->

Q: В каких случаях в Spring Data выполняются constraints каскадирования, типа `@OneToMany(mappedBy = "user", cascade = CascadeType.ALL)`?
A: Spring Data помимо использования Hibernate генерирует и прямые SQL запросы.
	
Поэтому, каскадирование будет работать только в случае, когда мы работаем с самим Entity:
	
```java
userRepo.deleteById(userId);  // ❌ Bypasses JPA cascade - direct SQL DELETE
```
	
- This executes a direct SQL DELETE statement without loading the entity or its relationships, so JPA cascade is ignored. The database foreign key constraint catches it.
	
```java
User user = userRepo.findById(userId).orElseThrow();
userRepo.delete(user);  // ✅ JPA cascade works
```
	
---
**Why This Happens**
	
- `deleteById(id)`→ Direct SQL:  `DELETE FROM users WHERE id = ?`
	
- `delete(entity)`→ JPA processes cascade → Deletes workouts first → Then deletes user
<!--ID: 1772268038702-->


Q: Чем `CrudRepository` отличается от `JpaRepository` в Spring Data JPA?  
A: `CrudRepository` предоставляет базовые CRUD-операции (`save`, `findById`, `findAll`, `delete` и др.) и не зависит от JPA. 
`JpaRepository` расширяет `PagingAndSortingRepository` и `CrudRepository`, добавляя JPA-специфичные возможности: пакетное сохранение и удаление (`saveAll`, `deleteAllInBatch`), принудительную синхронизацию с БД (`flush`, `saveAndFlush`), работу с пакетными операциями и удобные методы для пагинации и сортировки. На практике в Spring Boot приложениях чаще используют `JpaRepository`, так как он включает весь функционал `CrudRepository` и предоставляет дополнительные возможности JPA.
<!--ID: 1781024691504-->


Q: Какой интерфейс должен расширять интерфейс твоего репозитория, чтобы иметь возможность фильтровать по `Specification`?
A: `JpaSpecificationExecutor<MyEntity>`
<!--ID: 1781024691510-->
