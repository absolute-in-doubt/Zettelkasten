
28-02-2026 18:19

Status:

Tags: [[Spring Data JPA]] [[Spring]]

---
# @Modifying - Spring Data

**`@Modifying`** — аннотация Spring Data JPA для **DML-запросов** (`UPDATE`, `INSERT`, `DELETE`) и **DDL** в методах репозитория с `@Query`. **Обязательна** для `@Query` методов, изменяющих данные.

**~={red}Ставится ИСКЛЮЧИТЕЛЬНО на методах репозитория=~**:

```java
public interface UserRepository extends JpaRepository<User, Long> {
    
    // UPDATE
    @Modifying
    @Query("UPDATE User u SET u.active = false WHERE u.lastLogin < :date")
    int deactivateInactiveUsers(@Param("date") LocalDateTime date);
    
    // DELETE  
    @Modifying
    @Query("DELETE FROM User u WHERE u.email LIKE %:domain%")
    int deleteUsersByDomain(@Param("domain") String domain);
    
    // INSERT
    @Modifying
    @Query("INSERT INTO User (name, email) VALUES (:name, :email)")
    int insertUser(@Param("name") String name, @Param("email") String email);
    
    // DDL (редко!)
    @Modifying
    @Query(value = "CREATE INDEX IF NOT EXISTS idx_user_email ON users(email)", nativeQuery = true)
    void createEmailIndex();
}

```

**❌ НЕ НУЖНА** для:

- `save()`, `delete()`, `deleteById()` — Spring Data сам определяет
    
- Query methods типа `deleteByName(String name)`
    
- SELECT запросы


---
### Параметры @Modifying

|Параметр|Дефолт|Описание|
|---|---|---|
|`clearAutomatically`|`false`|**Очищать** Persistence Context после запроса|
|`flushAutomatically`|`false`|**flush** перед запросом (для видимости изменений)|

#### Возвращаемые типы

|Тип возврата|Описание|
|---|---|
|`void`|Ничего не возвращает|
|`int`|**Количество изменённых строк** (рекомендуется)|
|`long`|Количество (для больших наборов)|



---
### Полные примеры с best practices

##### 1. UPDATE с очисткой контекста

```java
@Modifying(clearAutomatically = true)  // ✅ Очищает L1 кэш
@Query("UPDATE Workout w SET w.status = 'CANCELLED' WHERE w.id IN :ids")
int cancelWorkouts(@Param("ids") List<Long> ids);
```

##### 2. UPDATE с flush (цепочка изменений)

```java
@Modifying(flushAutomatically = true, clearAutomatically = true)
@Transactional
@Query("UPDATE User u SET u.plan = :plan WHERE u.active = true")
int updateActiveUsersPlan(@Param("plan") Plan plan);
```

##### 3. Батч DELETE

```java
@Modifying
@Query("DELETE FROM Comment c WHERE c.post.id = :postId")
int deleteCommentsByPost(@Param("postId") Long postId);
```


---
## ⚠️ Важные особенности

## 1. **@Transactional обязательно**

```java
@Service
@Transactional  // ← Для методов сервиса
public class UserService {
    public int deactivateUsers(LocalDateTime date) {
        return userRepository.deactivateInactiveUsers(date);
    }
}
```

## 2. **clearAutomatically = true обязательно**

Без очистки **грязные объекты** в L1 кэше могут перезаписать изменения:

```java
// ❌ Проблема
user.setActive(true);  // В L1 кэше
repository.deactivateInactiveUsers(date);  // UPDATE active=false
userRepository.save(user);  // Перезаписывает обратно active=true!
```

## 3. **flushAutomatically для видимости**

```java
// Сначала сохраняем новый объект
workoutRepository.save(newWorkout);

// Затем обновляем связанные
@Modifying(flushAutomatically = true)  // Видит новую запись
@Query("UPDATE Plan p SET p.totalDuration = p.totalDuration + :duration WHERE p.id = :planId")
void incrementPlanDuration(@Param("planId") Long planId, @Param("duration") Duration duration);
```


----
#### [[@Modifying - Spring Data - Flashcards|Link to flashcards]]



---
### References:

