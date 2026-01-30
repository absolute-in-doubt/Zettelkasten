
Theory for the cards: 

FILE TAGS: spring

Q: В чём отличия аннотаций @Component, @Service, @Repository?
A: @Service - маркерная аннотация, делает то же самое, что и @Component (регистрирует в контексте)
	
**@Repository** отличается от **@Component** тем, что помиом регистрации в контексте, оборачивает выкидываемые `SQLException` в unchecked `DataAccessException`.
<!--ID: 1769527450047-->
