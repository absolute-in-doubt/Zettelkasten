
27-10-2025 19:47

Status: #baby 

Tags: [[Hibernate & JPA]]

---
# Session - Hibernate

Объект для взаимодействия с Hibernate.

Все основные действия с БД проиводятся именно через Session.

Является реализуемым классом Hibernate (класс, который, согласно стандарту JPA, должен быть реализован ORM фреймворком). 

В JPA - хранится в [[EntityManager - JPA|EntityManager]].

~={orange}**В приоритете - использовать [[EntityManager - JPA|EntityManager]].**=~

---
## Когда использовать:

- `Session` — это **нативный API Hibernate**, который предоставляет больше возможностей, чем JPA:
    
    - **ScrollableResults** для потоковой обработки больших выборок.
        
    - **StatelessSession** для batch‑операций без кэша первого уровня.
        
    - **Нативные SQL‑запросы** и специфичные для Hibernate функции.
        
    - Более тонкий контроль над flush/dirty checking.
        
- Эти возможности полезны, если вы упираетесь в ограничения JPA.


---
### Создание (получение) Session

```java
SessionFactory sessionFactory = configuration.buildSessionFactory();

Session session = sessionFactory.getCurrentSession();
Session session = sessionFactory.openSession(); // управление сессией вручную - он не закрывается апосле коммита транзакции

session.close();//обязательно только при ручном управлении сессией
```

> [!warning]
> Session закрывается после коммита транзакции:
> ```java
> session.getTransaction().commit(); //закроет эту session
> ```

---
### Методы Session

- `Serializable` ~={green}save(`Object objToBeSaved`)=~ - сразу возвращает id сохраняемого объекта

- ~={green}persist(`Object objToBeSaved`)=~ - для получения первичного ключа придётся вызвать getId(), он получается ==lazily== 

![[Pasted image 20251029132617.png]]

- update
- get
- ...

- `Query` ~={green}createQuery(`String HQLQuery`)=~ - создаёт TypedQuery



---
### Hibernate-native методы:

~={orange}Hibernate 6.0 - Deprecated=~

~={red}Hibernate 7.0 - вообще убрали

Сейчас используются только методы, определённые в JPA.

----
#### [[Session - Hibernate - Flashcards|Link to flashcards]]



---
### References:

