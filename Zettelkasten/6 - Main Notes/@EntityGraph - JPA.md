
27-01-2026 14:32

Status:

Tags: [[Hibernate & JPA]] [[Java+]]

---
# @EntityGraph - JPA

**EntityGraph в JPA** — механизм (JPA 2.1+) для динамического управления загрузкой связанных сущностей, заменяющий статичные `FetchType.LAZY/EAGER`. Позволяет точно контролировать fetch-граф во время выполнения.

## Зачем нужен

- ✅ **Избегает N+1 проблемы** — загружает связи одним JOIN запросом
    
- ✅ **Гибкость** — разные графы для разных запросов
    
- ✅ **Производительность** — только нужные данные, без Cartesian Product
    
- ❌ **FetchType.EAGER** — всегда всё, медленно
    
- ❌ **JOIN FETCH** — статично, везде одинаково


### Когда использовать

1. **Сложные DTO** — разные проекции (с author/без)
    
2. **API endpoints** — `/posts?withComments=true`
    
3. **Разные клиенты** — Admin видит всё, User — минимум
    
4. **Админки vs публичные API**
	
5. Для сложных pageable запросов с JOIN


### Типы EntityGraph

1. **Named** — `@NamedEntityGraph` в Entity
    
2. **Dynamic** — `em.createEntityGraph()`
    
3. **Fetch/Sub** — `FetchGraph` (LAZY→EAGER), `LoadGraph` (EAGER→LAZY)


---
## Примеры

#### 1. Named EntityGraph (Entity)

```java
@Entity
@NamedEntityGraphs({
    @NamedEntityGraph(
        name = "Post.withComments", 
        attributeNodes = @NamedAttributeNode("comments")
    ),
    @NamedEntityGraph(
        name = "Post.full", 
        attributeNodes = {
            @NamedAttributeNode("comments"),
            @NamedAttributeNode(value = "author", subgraph = "author.details")
        },
        subgraphs = @NamedSubgraph(
            name = "author.details", 
            attributeNodes = @NamedAttributeNode("roles")
        )
    )
})
public class Post {
    @OneToMany(fetch = FetchType.LAZY) 
    private List<Comment> comments;
    
    @ManyToOne(fetch = FetchType.LAZY) 
    private User author;
}
```


#### 2. Spring Data JPA

```java
@Repository
public interface PostRepository extends JpaRepository<Post, Long> {
    @EntityGraph(value = "Post.withComments", type = EntityGraph.EntityGraphType.FETCH)
    List<Post> findByPublishedTrue();
    
    @EntityGraph(attributePaths = {"author", "comments"})
    Optional<Post> findWithRelationsById(Long id);
}
```


#### 3. EntityManager (чистый JPA)

```java
EntityGraph<Post> graph = em.getEntityGraph("Post.full");
Map<String, Object> props = Map.of("jakarta.persistence.fetchgraph", graph);
Post post = em.find(Post.class, 1L, props);
```


#### 4. Dynamic Graph

```java
EntityGraph<Post> graph = em.createEntityGraph(Post.class);
graph.addAttributeNodes("author");
graph.addSubgraph("comments").addAttributeNodes("author");
```

----
#### [[EntityGraph - JPA - Flashcards|Link to flashcards]]



---
### References:

