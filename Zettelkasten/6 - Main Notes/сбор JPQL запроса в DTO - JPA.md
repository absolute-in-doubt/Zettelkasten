
25-05-2026 17:36

Status:

Tags: [[Hibernate & JPA]]

---
# сбор JPQL запроса в DTO - JPA

```java
public interface UserRepository extends JpaRepository<User, Long> {

    @Query("SELECT new com.example.dto.UserResultDto(" +
           "u.id, u.name, u.age, u.status) " +
           "FROM User u " +
           "WHERE (:name IS NULL OR u.name LIKE %:name%) " +
           "AND (:minAge IS NULL OR u.age >= :minAge) " +
           "AND (:maxAge IS NULL OR u.age <= :maxAge) " +
           "AND (:status IS NULL OR u.status = :status) " +
           "ORDER BY " +
           "CASE WHEN :sortBy = 'name' THEN u.name END :sortDirection, " +
           "CASE WHEN :sortBy = 'age' THEN u.age END :sortDirection, " +
           "CASE WHEN :sortBy = 'email' THEN u.email END :sortDirection")
    List<UserResultDto> findUsersByFilter(
        @Param("name") String name,
        @Param("minAge") Integer minAge,
        @Param("maxAge") Integer maxAge,
        @Param("status") Status status,
        @Param("sortBy") String sortBy,
        @Param("sortDirection") SortDirection sortDirection
    );
}
```


----
#### [[сбор JPQL запроса в DTO - JPA - Flashcards|Link to flashcards]]



---
### References:

