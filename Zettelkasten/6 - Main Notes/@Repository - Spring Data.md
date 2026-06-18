
08-11-2025 12:06

Status: #child

Tags: [[Spring Data JPA]] [[Spring]]

---
# @Repository - Spring Data

Создаём интерфейс, расширяющий `JpaRepository<EntityType, PrimaryKeyType>`:

~={purple}@Repository=~ - обозначает, что это JpaRepository

> [!tip] Note
> **~={purple}@Repository=~** отличается от **~={purple}@Component=~** тем, что помиом регистрации в контексте, оборачивает выкидываемые `SQLException` в unchecked `DataAccessException`.


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
### **Каскадирование в Spring Data**

Каскадирование работает только  при загрузке Entity [[Кэширование - JPA|L1 cash]].

```java
userRepo.deleteById(userId);  // ❌ Bypasses JPA cascade - direct SQL DELETE
```

This executes a direct SQL DELETE statement without loading the entity or its relationships, so JPA cascade is ignored. The database foreign key constraint catches it.

```java
User user = userRepo.findById(userId).orElseThrow();
userRepo.delete(user);  // ✅ JPA cascade works
```
	
---
##### **Why This Happens**

- `deleteById(id)`→ Direct SQL:  `DELETE FROM users WHERE id = ?`
	
- `delete(entity)`→ JPA processes cascade → Deletes workouts first → Then deletes user


----
#### [[@Repository - Spring Data - Flashcards|Link to flashcards]]



---
### References:

- [[setup - Spring Data]]