
28-05-2026 18:43

Status:

Tags: [[Hibernate & JPA]] [[Java+]]

---
# Денормализованные поля Entity - JPA


```java
@JdbcTypeCode(SqlTypes.JSON)  
@Column(name = "roles", columnDefinition = "jsonb", nullable = false)  
private List<com.example.fitness.tracker.enums.Role> roles;
```

----
#### [[Денормализованные поля Entity - JPA - Flashcards|Link to flashcards]]



---
### References:

