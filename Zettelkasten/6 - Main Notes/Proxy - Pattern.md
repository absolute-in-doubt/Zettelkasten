
21-02-2026 20:02

Status:

Tags: [[Patterns]]

---
# Proxy - Pattern

Контролирует доступ к объекту.



### Пример в Spring

Dynamic proxy (CGLib). Создаётся на этапе postProcessAfterInitialization.

Наиболее наглядная логика - логика аннотаций @PreAuthorize и @Cacheable:


**`@PreAuthorize`** и **`@PostAuthorize`** — проверяют права **до** или **после** вызова метода.

```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long id) { ... }

@PostAuthorize("returnObject.owner == authentication.name")
public User getUser(Long id) { ... }
```

Прокси блокирует вызов, если условия не выполнены — чистый контроль доступа.​

## Spring Cache аннотации

**`@Cacheable`**, **`@CacheEvict`** — прокси контролирует, **нужен ли** вызов метода.

```java
@Cacheable("users")
public User findUser(Long id) { ... }
```


----
#### [[Proxy - Pattern - Flashcards|Link to flashcards]]



---
### References:

