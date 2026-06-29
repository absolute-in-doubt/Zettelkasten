
27-06-2026 16:06

Status:

Tags: [[Spring Data Access]] [[Spring]]

---
# Using Repositories with Multiple Spring Data Modules - Spring Data Access

Допустим, в проекте подключено несколько Spring Data модулей: Spring Data JPA и Spring Data MongoDB.

**~={orange}Domain class  <-> entity=~** 
(просто не везде они называются entity, например, в MongoDB это documents)


When it detects multiple repository factories on the class path, Spring Data enters strict repository configuration mode. Strict configuration uses details on the repository or the domain class to decide about Spring Data module binding for a repository definition:


1. If the repository definition [extends the module-specific repository](https://docs.spring.io/spring-data/mongodb/reference/repositories/definition.html#repositories.multiple-modules.types), it is a valid candidate for the particular Spring Data module.

```java
interface MyRepository extends JpaRepository<User, Long> { }

@NoRepositoryBean
interface MyBaseRepository<T, ID> extends JpaRepository<T, ID> { … }

interface UserRepository extends MyBaseRepository<User, Long> { … }
/**
`MyRepository` and `UserRepository` extend `JpaRepository` in their type hierarchy. They are valid candidates for the Spring Data JPA module.
**/
```

Repository definitions using generic interfaces:

```java
interface AmbiguousRepository extends Repository<User, Long> { … }

@NoRepositoryBean
interface MyBaseRepository<T, ID> extends CrudRepository<T, ID> { … }

interface AmbiguousUserRepository extends MyBaseRepository<User, Long> { … }

/**
`AmbiguousRepository` and `AmbiguousUserRepository` extend only `Repository` and `CrudRepository` in their type hierarchy. While this is fine when using a unique Spring Data module, multiple modules cannot distinguish to which particular Spring Data these repositories should be bound.
**/
```


    
2. If the ~={orange}domain class=~ is [annotated with the module-specific type annotation](https://docs.spring.io/spring-data/mongodb/reference/repositories/definition.html#repositories.multiple-modules.annotations), it is a valid candidate for the particular Spring Data module. Spring Data modules accept either third-party annotations (such as JPA’s `@Entity`) or provide their own annotations (such as `@Document` for Spring Data MongoDB and Spring Data Elasticsearch).

```java
interface PersonRepository extends Repository<Person, Long> { … }

@Entity
class Person { … }

interface UserRepository extends Repository<User, Long> { … }

@Document
class User { … }

/**
`PersonRepository` references `Person`, which is annotated with the JPA `@Entity` annotation, so this repository clearly belongs to Spring Data JPA. 

`UserRepository` references `User`, which is annotated with Spring Data MongoDB’s `@Document` annotation.
**/
```

> [!warning]
> This is a bad practice:
> ```java
> interface JpaPersonRepository extends Repository<Person, Long> { … }
>
>interface MongoDBPersonRepository extends Repository<Person, Long> { … }
>
>@Entity
>@Document
>class Person { … }
> ```
> 
> This example shows a domain class using both JPA and Spring Data MongoDB annotations. It defines two repositories, `JpaPersonRepository` and `MongoDBPersonRepository`. 
> 
> One is intended for JPA and the other for MongoDB usage. 
> 
> Spring Data is no longer able to tell the repositories apart, which leads to undefined behavior.


3. Если всё же необходимо использовать один и тот же domain class и как Document и как Entity - явно указываем пути для сканирования пакетов для разных модулей Spring Data:

```java
@EnableJpaRepositories(basePackages = "com.acme.repositories.jpa")
@EnableMongoRepositories(basePackages = "com.acme.repositories.mongo")
class Configuration { … }
```

----
#### [[Using Repositories with Multiple Spring Data Modules - Spring Data Access - Flashcards|Link to flashcards]]



---
### References:

