
29-10-2025 20:13

Status: #baby 

Tags: [[Hibernate & JPA]]

---
# Каскадирование - Hibernate

В Hibernate доступно [[Каскадирование - JPA|каскадирование из JPA]], но оно не подходит для Hibernate-native методов класса [[Session - Hibernate|Session]], таких как `save(...)`.

Для этого существуют аннотаиции каскадирования Hibernate.

> [!warning]
> ~={red}**Все аннотации каскадирования Hibernate - Deprecated. **=~
> Используем стандартные аннотации JPA.

---

### @Cascade(CascadeType.{SOME_TYPE})

указывается тип каскадирования для поля:

Пример:
```java
@Cascade(org.hibernate.annotations.CascadeType.SAVE_UPDATE)
```

Теперь это заменяется двумя типами:
```java
@OneToMany(cascade = {CascadeType.PERSIST, CascadeType.MERGE})
```


### ~={orange}Hibernate 6.0 - Deprecated=~

### ~={red}Hibernate 7.0 - вообще убрали=~


----
#### [[Каскадирование - Hibernate - Flashcards|Link to flashcards]]



---
### References:

