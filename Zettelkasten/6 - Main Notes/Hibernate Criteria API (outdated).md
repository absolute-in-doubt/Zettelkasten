
04-11-2025 15:26

Status: #not_even_born 

Tags: [[Hibernate & JPA]]

---
# Hibernate Criteria API (outdated)

**Идея:** вместо того чтобы писать строку `"SELECT u FROM User u WHERE u.age > 18"`, вы строите запрос через объекты и методы Java.

```java
Criteria crit = session.createCriteria(User.class);
crit.add(Restrictions.gt("age", 18));
List<User> results = crit.list();
```

## 🔎 Для чего они нужны

1. **Динамические запросы** Когда условия формируются во время выполнения (например, фильтры в поисковой форме), Criteria API удобнее, чем конкатенация строк HQL/SQL.

2. **Безопасность и рефакторинг** Ошибки в названиях полей легче отлавливать на этапе компиляции (особенно с метамоделью JPA), чем в строковых запросах.
   
3. **Интеграция с IDE и типобезопасность** IDE подсказывает доступные поля сущностей, что снижает риск опечаток.
   
4. **Портируемость** Код не привязан к конкретному диалекту SQL, Hibernate сам генерирует SQL под нужную СУБД.


----
#### [[Hibernate Criteria API (outdated) - Flashcards|Link to flashcards]]



---
### References:

- [[JPA Criteria API]]