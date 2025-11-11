
02-11-2025 10:54

Status: #not_even_born 

Tags: [[Hibernate & JPA]]

---
# HQL запросы - JPA

```java
session.getTransaction().begin();  

session.createQuery("UPDATE Person SET age=18 WHERE name LIKE 'name-%'").executeUpdate();  

session.createQuery("DELETE FROM Person WHERE age<18").executeUpdate();  

List<Person> people = session.createQuery("FROM Person WHERE name LIKE 'name-%'").getResultList();  

for(Person pers : people){  
    System.out.println(pers);  
}  

session.getTransaction().commit();
```


----
#### [[HQL запросы - JPA - Flashcards|Link to flashcards]]



---
### References:

