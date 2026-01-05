
04-11-2025 15:29

Status: #baby 

Tags: [[Hibernate & JPA]]

---
# JPA Criteria API

**Идея:** вместо того чтобы писать строку `"SELECT u FROM User u WHERE u.age > 18"`, вы строите запрос через объекты и методы Java.



With pure JPA interfaces (using EntityManager):
```java
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<User> cq = cb.createQuery(User.class);
Root<User> root = cq.from(User.class);
cq.select(root).where(cb.gt(root.get("age"), 18));
List<User> results = em.createQuery(cq).getResultList();
```

#### [[Методы задания условий для JPA Criteria API запросов]]


> [!note] 
> Hibernate поддерживает реализацию JPA Criteria API (т.к. это часть JPA)

Пример с Session:
```java
session = sessionFactory.getCurrentSession();  
Transaction transaction = session.beginTransaction();  
CriteriaBuilder criteriaBuilder = session.getCriteriaBuilder();  
//создаём запрос, указывая, что он возвратит объекты класса Developer
CriteriaQuery<Developer> criteria = criteriaBuilder.createQuery(Developer.class);  
Root<Developer> devCriteria = criteria.from(Developer.class);  
List <Developer> developers = session.createQuery(criteria).getResultList();  
transaction.commit();  
session.close();
```

`CriteriaQuery` - отвечает за создание SELECT запросов для определённого типа данных.

`Root<X>` — корневой объект, от которого начинается выборка (аналог `FROM X` в SQL/JPQL).

##### Условия при поиске:

```java
criteria.select(devCriteria).where(criteriaBuilder.like(devCriteria.get("name"), String.format("%%%s%%", nameSubstr)));

List<Developer> developers = session.createQuery(criteria).getResultList();
```


---
## 🔎 Для чего они нужны

1. **Динамические запросы** Когда условия формируются во время выполнения (например, фильтры в поисковой форме), Criteria API удобнее, чем конкатенация строк HQL/SQL.

2. **Безопасность и рефакторинг** Ошибки в названиях полей легче отлавливать на этапе компиляции (особенно с метамоделью JPA), чем в строковых запросах.
   
3. **Интеграция с IDE и типобезопасность** IDE подсказывает доступные поля сущностей, что снижает риск опечаток.
   
4. **Портируемость** Код не привязан к конкретному диалекту SQL, Hibernate сам генерирует SQL под нужную СУБД.




----
#### [[JPA Criteria API - Flashcards|Link to flashcards]]



---
### References:

- [[Hibernate Criteria API (outdated)]]