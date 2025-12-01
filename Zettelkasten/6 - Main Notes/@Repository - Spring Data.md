
08-11-2025 12:06

Status: #child

Tags: [[Spring]]

---
# @Repository - Spring Data

Создаём интерфейс, расширяющий `JpaRepository<EntityType, PrimaryKeyType>`:

~={purple}@Repository=~ - обозначает, что это JpaRepository

```java
@Repository
public interface PeopleJpaRepository extends JpaRepository<Person, Integer> {  
}
```


---
### Методы, создаваемые JpaRepository:

```java
public interface JpaRepository<T, ID> extends ListCrudRepository<T, ID>, ListPagingAndSortingRepository<T, ID>, QueryByExampleExecutor<T> {  

    void flush();  
	  
    <S extends T> S saveAndFlush(S entity);  
	  
    <S extends T> List<S> saveAllAndFlush(Iterable<S> entities);  
	  
    /** @deprecated */  
    @Deprecated  
    default void deleteInBatch(Iterable<T> entities) {  
        this.deleteAllInBatch(entities);  
    }  
	  
    void deleteAllInBatch(Iterable<T> entities);  
	  
    void deleteAllByIdInBatch(Iterable<ID> ids);  
	  
    void deleteAllInBatch();  
	  
    /** @deprecated */  
    @Deprecated  
    T getOne(ID id);  
	  
    /** @deprecated */  
    @Deprecated  
    T getById(ID id);  
	  
    T getReferenceById(ID id);  
	  
    <S extends T> List<S> findAll(Example<S> example);  
	  
    <S extends T> List<S> findAll(Example<S> example, Sort sort);  
}
```


> [!note]
> В Spring Data есть соглашение, что для обновления объекта в БД используется тот же метод save, как и для сохранения. 
> 
> Т.е. мы поставим тому объекту, что хотим сохранить тот же id, то у изначального объекта в БД и передадим в метод save(). 
> Spring Data его просто merge()-ит.
> 
> ```java
> @Transactional  
>public void update(Person person, int id){  
>    person.setId(id);  
>    peopleRepo.save(person);  
>}
> ```

----
#### [[@Repository - Spring Data - Flashcards|Link to flashcards]]



---
### References:

- [[setup - Spring Data]]