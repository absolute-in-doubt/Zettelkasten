
Theory for the cards: [[EntityManager - JPA]] 

FILE TAGS: jpa

Q: Являются ли `EntitiyManager` и `EntityManagerFactory` thread-safe?
A:   
- `EntityManagerFactory` - thread-safe
- `EntityManager` - thread-unsafe
<!--ID: 1769430803665-->


Q: Будет ли здесь объект удалён?
```java
entityManager.remove(obj1);
entityManager.detach(obj1);
entityMAnager.getTransaction().commit();
```
A:   
-  remove(`Object entity`) - marking entity for removal
	
- detach(`Object entity`) - удаляет объект из Context. Unflushed changes will be lost.
	
Объект `obj1` НЕ БУДЕТ УДАЛЁН, т.к. мы его пометили для удаления, но после этого отключили помеченный объект от контекста.
<!--ID: 1769430803675-->
