
26-02-2026 18:28

Status:

Tags: [[Spring Data JPA]] [[Spring]]

---
# Specification - Spring Data



**Specifications** — это типобезопасный API для динамического построения Criteria-запросов. Позволяет комбинировать условия через `and()`, `or()`, `not()` для REST API с множеством фильтров.

### Настройка репозитория

```java
public interface UserRepository extends 
    JpaRepository<User, Long>, 
    JpaSpecificationExecutor<User>  // ← Обязательный интерфейс
{}
```

### Базовая структура Specification

```java
public interface Specification<T> {
    Predicate toPredicate(
	    Root<T> root, 
	    CriteriaQuery<?> query, 
	    CriteriaBuilder cb
	);
}
```

**Параметры**:

- `Root<T> root` — корень запроса (сущность)
    
- `CriteriaQuery<?> query` — сам запрос (для `order by`, `group by`)
    
- `CriteriaBuilder cb` — фабрика предикатов (условия)
   

---
## Примеры


### Создание базовых спецификаций

```java
public class UserSpecifications {
    
    // Равенство
    public static Specification<User> hasFirstName(String name) {
        return (root, query, cb) -> cb.equal(root.get("firstName"), name);
    }
    
    // LIKE
    public static Specification<User> nameContains(String name) {
        return (root, query, cb) -> 
            cb.like(cb.lower(root.get("firstName")), 
                   "%" + name.toLowerCase() + "%");
    }
    
    // IN
    public static Specification<User> hasStatuses(List<Status> statuses) {
    return (root, query, cb) ->
            root.get("status").in(statuses);
	}
    
    // Диапазон
    public static Specification<User> ageBetween(int min, int max) {
        return (root, query, cb) -> 
            cb.between(root.get("age"), min, max);
    }
    
    // NULL проверка
    public static Specification<User> hasEmail() {
        return (root, query, cb) -> 
            cb.isNotNull(root.get("email"));
    }
}
```

### Комбинирование спецификаций

```java
// AND
Specification<User> spec = UserSpecifications.nameContains("john")
    .and(UserSpecifications.ageBetween(18, 65));

// OR  
Specification<User> spec = UserSpecifications.hasFirstName("John")
    .or(UserSpecifications.hasFirstName("Jane"));

// NOT
Specification<User> spec = UserSpecifications.nameContains("john").not();
```

### JOIN'ы в Specifications

```java
// JOIN с сущностью One-to-Many
public static Specification<User> hasActiveOrders() {
    return (root, query, cb) -> {
        Join<User, Order> orders = root.join("orders", JoinType.INNER);
        return cb.equal(orders.get("status"), OrderStatus.ACTIVE);
    };
}

// JOIN с условием
public static Specification<User> hasOrderByDate(LocalDateTime date) {
    return (root, query, cb) -> {
        Join<User, Order> orders = root.join("orders");
        return cb.greaterThan(orders.get("createdAt"), date);
    };
}
```

## Сортировка и группировка

```java
Specification<User> spec = UserSpecifications.nameContains("john");

// Добавляем сортировку
Pageable pageable = PageRequest.of(0, 10, Sort.by("age").descending());

// Использование
Page<User> result = userRepository.findAll(spec, pageable);
```


### Полный пример для REST API

```java
@RestController
public class UserController {
    
    @GetMapping("/users")
    public Page<User> findUsers(UserFilter filter, Pageable pageable) {
        Specification<User> spec = Specification.where(null);
        
        if (filter.getName() != null) {
            spec = spec.and(UserSpecifications.nameContains(filter.getName()));
        }
        if (filter.getMinAge() != null) {
            spec = spec.and(UserSpecifications.ageGreaterThan(filter.getMinAge()));
        }
        if (!filter.getStatuses().isEmpty()) {
            spec = spec.and(hasStatusIn(filter.getStatuses()));
        }
        
        return userRepository.findAll(spec, pageable);
    }
}
```



---
## Продвинутые техники

#### 1. Динамические JOIN'ы (избегаем Cartesian Product)

```java
public static Specification<User> withDistinctOrders() {
    return (root, query, cb) -> {
        if (!query.getResultType().equals(Long.class)) {
            root.fetch("orders", JoinType.LEFT);
            query.distinct(true);
        }
        return cb.conjunction(); // всегда true
    };
}
```


#### 2. Подзапросы

```java
public static Specification<User> hasMoreOrdersThanAvg() {
    return (root, query, cb) -> {
        Subquery<Long> subquery = query.subquery(Long.class);
        Root<Order> orderRoot = subquery.from(Order.class);
        subquery.select(cb.count(orderRoot))
                .where(cb.equal(orderRoot.get("user"), root));
        
        return cb.greaterThan(
            cb.size(root.get("orders")), 
            cb.avg(subquery)
        );
    };
}
```

## 3. Игнорирование case sensitivity

```java
public static Specification<User> nameInsensitiveContains(String name) {
    return (root, query, cb) -> 
        cb.like(cb.lower(root.get("firstName")), 
               "%" + name.toLowerCase() + "%");
}
```

## [[Методы задания условий для JPA Criteria API запросов|Методы CriteriaBuilder]]


----
#### [[Specification - Spring Data - Flashcards|Link to flashcards]]



---
### References:

- [[JPA Criteria API]]
- [[Методы задания условий для JPA Criteria API запросов]]
- [[Пагинация- Spring Data]]