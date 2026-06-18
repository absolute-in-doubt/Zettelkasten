
04-01-2026 16:18

Status: #adult

Tags: [[Hibernate & JPA]] [[Java+]]

---
# Методы задания условий для JPA Criteria API запросов

### Условия (Predicates)

- `cb.equal(path, value)` — равенство (`=`)
- `cb.notEqual(path, value)` — не равно (`<>`)
- `cb.greaterThan(path, value)` — больше (`>`) для сравн. типов
- `cb.greaterThanOrEqualTo(path, value)` — больше либо равно (`>=`)
- `cb.lessThan(path, value)` — меньше (`<`)
- `cb.lessThanOrEqualTo(path, value)` — меньше либо равно (`<=`)
- `cb.like(path, pattern)` — `LIKE` для строк (`%` и `_`)
- `cb.notLike(path, pattern)` — отрицание `LIKE`
- `cb.between(path, value1, value2)` — `BETWEEN`
- `cb.isNull(path)` / `cb.isNotNull(path)` — `IS NULL` / `IS NOT NULL`
- `cb.in(path)` — `IN (...)` (обычно с `inPredicate.value(x)`)

### Логическое комбинирование условий

- `cb.and(predicate1, predicate2, ...)` — логическое AND
- `cb.or(predicate1, predicate2, ...)` — логическое OR
- `cb.not(predicate)` — логическое NOT

### WHERE в CriteriaQuery

- `cq.where(predicate)` — задать одно условие
- `cq.where(cb.and(...))` / `cq.where(cb.or(...))` — сложные условия

### Сортировка (ORDER BY)

- `cb.asc(path)` — сортировка по возрастанию
- `cb.desc(path)` — сортировка по убыванию
- `cq.orderBy(cb.asc(root.get("name")))`
- `cq.orderBy(cb.asc(...), cb.desc(...))` — несколько полей

### Группировка (GROUP BY / HAVING)

- `cq.groupBy(root.get("status"), root.get("type"))`
- `cq.having(predicate)` — условия на группы (аналог HAVING)
  - обычно с агрегатами: `cb.gt(cb.count(root), 1L)` и т.п.

### Агрегатные функции

- `cb.count(path)` / `cb.countDistinct(path)`
- `cb.sum(path)` / `cb.avg(path)`
- `cb.max(path)` / `cb.min(path)`

### Типичный шаблон

1. `CriteriaBuilder cb = em.getCriteriaBuilder();`
2. `CriteriaQuery<Entity> cq = cb.createQuery(Entity.class);`
3. `Root<Entity> root = cq.from(Entity.class);`
4. Создаём `Predicate` через `cb.*` и передаём в:
   - `cq.where(predicate);`
   - `cq.orderBy(...);`
   - `cq.groupBy(...);`
   - `cq.having(...);`
5. `List<Entity> result = em.createQuery(cq).getResultList();`


----
#### [[Методы задания условий для JPA Criteria API запросов - Flashcards|Link to flashcards]]



---
### References:

- [[JPA Criteria API]]
- [[Specification - Spring Data]]