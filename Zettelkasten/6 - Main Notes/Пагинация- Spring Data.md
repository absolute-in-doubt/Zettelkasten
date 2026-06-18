
26-02-2026 19:27

Status:

Tags: [[Spring Data JPA]] [[Spring]]

---
# Пагинация - Spring Data


### Основные интерфейсы

#### `Pageable` (запрос страницы)

```java
Pageable pageable = PageRequest.of(page, size, sort);
```

**Параметры**:

- `page` — номер страницы (0-based)
    
- `size` — размер страницы
    
- `sort` — сортировка (`Sort.by("field").descending()`)


---
## `Page<T>` (результат)

```java
Page<T> page = repository.findAll(pageable);
```

**Методы**:

- `getContent()` — список элементов
    
- `getTotalElements()` — общее количество
    
- `getTotalPages()` — общее количество страниц
    
- `hasNext()`, `hasPrevious()`, `isFirst()`
    
- `getNumber()`, `getSize()`, `getNumberOfElements()`
    
- `getSortable()`, `getSort()`


### `Slice<T>`

##### Page vs Slice vs List

|Тип возврата|count(*) запрос|Методы|Когда использовать|
|---|---|---|---|
|`Page<T>`|✅ Есть|`getTotalPages()`, `getTotalElements()`|Нужен полный контроль пагинации|
|`Slice<T>`|❌ Нет|`hasNext()`, `hasPrevious()`|Только навигация, экономия запросов|
|`List<T>`|❌ Нет|`size()`, `isEmpty()`|Когда пагинация не нужна|

**Пример**:

```java
// Page — 2 SQL запроса (данные + COUNT)
Page<User> page = repo.findAll(pageable);

// Slice — 1 SQL запрос
Slice<User> slice = repo.findFirst5ByAgeGreaterThan(18, pageable);
```



---
### Создание Pageable

#### 1. Базовая пагинация

```java
// Страница 0 (первая), 10 элементов
Pageable pageable = PageRequest.of(0, 10);

// Страница 2, 20 элементов  
Pageable pageable = PageRequest.of(2, 20);
```

#### 2. С сортировкой

```java
// По одному полю
Pageable pageable = PageRequest.of(0, 10, Sort.by("name"));

// По нескольким полям
Pageable pageable = PageRequest.of(0, 10, 
    Sort.by("age").descending().and(Sort.by("name")));

// Другие варианты
Sort sort = Sort.by("firstName").ascending()
               .and(Sort.by("lastName").descending());
Pageable pageable = PageRequest.of(0, 10, sort);
```

### 3. Из HTTP параметров (Spring Web)

```java
@GetMapping("/users")
public Page<User> getUsers(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "10") int size,
    @RequestParam(defaultValue = "id") String sortBy,
    @RequestParam(defaultValue = "asc") String sortDir
) {
    Sort sort = sortDir.equalsIgnoreCase(Sort.Direction.ASC.name()) 
        ? Sort.by(sortBy).ascending() 
        : Sort.by(sortBy).descending();
    Pageable pageable = PageRequest.of(page, size, sort);
    return userRepository.findAll(pageable);
}
```

#### [[@PageableDefault - Spring Data|Или всего одной аннотацией @PagableDefault]]


----
## Использование в репозиториях

#### 1. Базовые методы

```java
public interface UserRepository extends JpaRepository<User, Long> {
    Page<User> findAll(Pageable pageable);
    Page<User> findByAgeGreaterThan(int age, Pageable pageable);
}
```

####  2. С Specifications


```java
public interface UserRepository extends 
    JpaRepository<User, Long>, 
    JpaSpecificationExecutor<User> { }

Page<User> result = userRepository.findAll(specification, pageable);
```

#### 3. С @Query

```java
@Query("SELECT u FROM User u WHERE u.status = :status")
Page<User> findByStatus(@Param("status") Status status, Pageable pageable);

@Query(value = "SELECT * FROM users WHERE age > ?1", nativeQuery = true)
Page<User> findUsersOlderThan(int age, Pageable pageable);
```



## Полный REST контроллер

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired private UserRepository userRepository;
    
    @GetMapping
    public ResponseEntity<Page<User>> getUsers(
            UserFilter filter,
            @PageableDefault(size = 10, sort = "id") Pageable pageable // дефолтные значения
    ) {
        Specification<User> spec = buildSpecification(filter);
        Page<User> users = userRepository.findAll(spec, pageable);
        
        return ResponseEntity.ok(users);
    }
    
    // DTO для фронтенда
    @GetMapping("/info")
    public Map<String, Object> getPageInfo(Pageable pageable) {
        return Map.of(
            "page", pageable.getPageNumber(),
            "size", pageable.getPageSize(),
            "sort", pageable.getSort()
        );
    }
}

```



----
#### [[Pageable - Spring Data - Flashcards|Link to flashcards]]



---
### References:

