
19-03-2026 18:00

Status:

Tags: [[Кэширование]] [[Spring Data JPA]]

---
# Пример кэширования с инвалидацией - Spring Data


```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    
    @Cacheable(value = "users", key = "#id")  // кэширует по ID
    Optional<User> findById(Long id);
    
    @Cacheable(value = "users", key = "#email")  // кэширует по email
    Optional<User> findByEmail(String email);
    
    @Cacheable(value = "users")  // кэш по всем параметрам (name, age)
    List<User> findByNameAndAge(String name, Integer age);
    
    @CachePut(value = "users", key = "#result.id")  // обновляет кэш
    @CacheEvict(value = "users", key = "#user.id")  // удаляет из кэша
    User save(User user);
    
    @CacheEvict(value = "users", key = "#id")
    void deleteById(Long id);
    
    @CacheEvict(value = "users", allEntries = true)  // очищает весь кэш users
    @Modifying
    @Query("DELETE FROM User u WHERE u.email = :email")
    void deleteByEmail(@Param("email") String email);
}
```


----
#### [[Пример кэширования с инвалидацией - Spring Data - Flashcards|Link to flashcards]]



---
### References:

