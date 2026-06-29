
27-06-2026 15:44

Status:

Tags: [[Spring Data MongoDB]] [[Spring Data Access]] [[Mongo DB]] [[Spring]]

---
# MongoRepository interface - Spring Data MongoDB


### `MongoRepository` 

`MongoRepository` extends [[Common Repository interfaces - Spring Data Access#`CrudRepository` & `ListCrudRepository`|CrudRepository]]. 

(технико-специфицированный интерфейс, как `JpaRepository` в Spring Data JPA)

Он расширяет базовые интерфейсы вроде
- `CrudRepository`, 
- `ListCrudRepository`, 
- `PagingAndSortingRepository` 
- `QueryByExampleExecutor`.

```java
public interface UserRepository extends MongoRepository<User, String> {
}
```


---

### Создание Domain class-specific interface

Чтобы создать репозиторий для своего доменного entity необходимо создать интерфейс, расширяющий `Repository<Entity, ID>`.

Можно взять его наследников:

- [[Common Repository interfaces - Spring Data Access#`CrudRepository` & `ListCrudRepository`|CrudRepository или ListCrudRepository]]

> [!note]
> If you are using a reactive store you might choose `ReactiveCrudRepository`, or `RxJava3CrudRepository` depending on which reactive framework you are using


Additionally you can extend 

- [[Common Repository interfaces - Spring Data Access#`PagingAndSortingRepository` & `ListPagingAndSortingRepository`|PagingAndSortingRepository или ListPagingAndSortingRepository]], 

`RxJava3SortingRepository`, 

> [!warning] Note 
> The various sorting repositories no longer extend their respective CRUD repository as they did in Spring Data versions prior to 3.0. Therefore, you need to extend both interfaces if you want functionality of both.
>
>Но они всё еще расширяют `Repository<Entity, ID>`.


----
#### [[MongoRepository interface - Spring Data MongoDB - Flashcards|Link to flashcards]]



---
### References:

