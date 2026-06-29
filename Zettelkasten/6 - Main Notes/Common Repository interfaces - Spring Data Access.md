
27-06-2026 15:56

Status:

Tags: [[Spring Data Access]] [[Spring]]

---
# Common Repository interfaces - Spring Data Access


### `CrudRepository` & `ListCrudRepository`

The  `CrudRepository` and `ListCrudRepository` interfaces provide sophisticated CRUD functionality for the entity class that is being managed.

```java
public interface CrudRepository<T, ID> extends Repository<T, ID> {

  <S extends T> S save(S entity);

  Optional<T> findById(ID primaryKey);

  Iterable<T> findAll();

  long count();

  void delete(T entity);

  boolean existsById(ID primaryKey);

  // … more functionality omitted.
}
```

`ListCrudRepository` offers equivalent methods, but they return `List` where the `CrudRepository` methods return an `Iterable`.


---
### `PagingAndSortingRepository` & `ListPagingAndSortingRepository`

In addition to `CrudRepository`, there are 
- `PagingAndSortingRepository`,
- `ListPagingAndSortingRepository` 
which add additional methods to ease paginated access to entities:

```java
interface PagingAndSortingRepository<T, ID> extends Repository<T, ID> {

  Iterable<T> findAll(Sort sort);

  Page<T> findAll(Pageable pageable);
}
```




----
#### [[Common Repository interfaces - Spring Data Access - Flashcards|Link to flashcards]]



---
### References:

- [[MongoRepository interface - Spring Data MongoDB]]