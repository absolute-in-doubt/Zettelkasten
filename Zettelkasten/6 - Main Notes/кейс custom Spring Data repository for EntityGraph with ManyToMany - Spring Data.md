
12-06-2026 20:30

Status:

Tags: [[Spring Data JPA]] [[Spring]]

---
# кейс custom Spring Data repository for EntityGraph with ManyToMany - Spring Data

для кастомной реализации репозитория используется связка:

1. **Интерфейс репозитория** (`OrderRepository`)
2. **Кастомный интерфейс** (`OrderRepositoryCustom`)
3. **Класс реализации** (`OrderRepositoryImpl`)



### Основной репозиторий

```java
@Repository
public interface OrderRepository
        extends JpaRepository<Order, Long>,
                JpaSpecificationExecutor<Order>,
                OrderRepositoryCustom {
}
```


---
### Кастомный интерфейс

Объявляем метод, которого нет в Spring Data:

```java
public interface OrderRepositoryCustom {

    List<Order> findAll(
            Specification<Order> specification,
            String entityGraphName
    );
}
```


---
### Реализация

Важно: имя должно быть

```text
OrderRepository + Impl
```

то есть:

```java
OrderRepositoryImpl
```

Тогда Spring Data автоматически её подцепит.

```java
@Repository
@RequiredArgsConstructor
public class OrderRepositoryImpl implements OrderRepositoryCustom {

    @PersistenceContext
    private final EntityManager entityManager;

    @Override
    public List<Order> findAll(
            Specification<Order> specification,
            String entityGraphName
    ) {

        CriteriaBuilder cb = entityManager.getCriteriaBuilder();

        CriteriaQuery<Order> query =
                cb.createQuery(Order.class);

        Root<Order> root = query.from(Order.class);

        Predicate predicate =
                specification.toPredicate(root, query, cb);

        query.where(predicate);

        TypedQuery<Order> typedQuery =
                entityManager.createQuery(query);

        EntityGraph<?> entityGraph =
                entityManager.getEntityGraph(entityGraphName);

        typedQuery.setHint(
                "jakarta.persistence.fetchgraph",
                entityGraph
        );

        return typedQuery.getResultList();
    }
}
```

Использование:

```java
List<Order> orders =
        orderRepository.findAll(
                specification,
                "order-with-items"
        );
```

---

## Более универсальный вариант

Чтобы не передавать имя графа строкой:

```java
public interface OrderRepositoryCustom {

    List<Order> findAll(
            Specification<Order> specification,
            EntityGraph<?> entityGraph
    );
}
```

Тогда:

```java
EntityGraph<Order> graph =
        entityManager.createEntityGraph(Order.class);

Subgraph<OrderItem> itemsGraph =
        graph.addSubgraph("items");

itemsGraph.addAttributeNodes("item");
```

И:

```java
orderRepository.findAll(specification, graph);
```

---

## Как это реализовано внутри Spring Data

На самом деле `JpaSpecificationExecutor` делает примерно следующее:

```java
CriteriaBuilder cb = em.getCriteriaBuilder();

CriteriaQuery<T> query = cb.createQuery(domainClass);

Root<T> root = query.from(domainClass);

Predicate predicate =
        specification.toPredicate(root, query, cb);

query.where(predicate);

return em.createQuery(query)
         .getResultList();
```

Ты просто добавляешь перед выполнением:

```java
query.setHint(
        "jakarta.persistence.fetchgraph",
        entityGraph
);
```

---

## Для Pageable

Если нужен аналог:

```java
Page<Order> findAll(
        Specification<Order> spec,
        Pageable pageable
);
```

то реализация становится заметно сложнее:

- отдельный запрос на данные;
    
- отдельный `countQuery`;
    
- применение сортировки;
    
- `setFirstResult()`;
    
- `setMaxResults()`.
    

Поэтому на реальных проектах часто делают кастомный базовый репозиторий (наследник `SimpleJpaRepository`) или используют готовую библиотеку для поддержки `EntityGraph + Specification + Pageable`. Для одного-двух методов обычно хватает показанной выше реализации.

----
#### [[кейс custom Spring Data repository for EntityGraph with ManyToMany - Spring Data - Flashcards|Link to flashcards]]



---
### References:

