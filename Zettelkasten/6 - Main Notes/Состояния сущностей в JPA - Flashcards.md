
Theory for the cards: [[Состояния сущностей в JPA]]

FILE TAGS: jpa

Q: Какие есть состояния сущностей в JPA?
A: **Transient (New) state**
	
Объект пока никак не связан с Hibernate и Hibernate его никак не отслеживает.
	
---
**Managed (Persistent) state**
	
Объект становится Managed тогда, когда подключается к контексту через
	
```java
entityManager.persist(obj1); //загрузит в Context
	
	
Person obj2 = session.find(Person.class, id); // возвратит managed instance
```
	
---
**Detached state**
	
Объект перестаёт быть Managed.
	
```java
entityManager.persist(obj1);
	
obj1.setName("Jake"); //тут объект ещё подключён к Context, это изменение отобразится в БД
	
entityManager.detach(obj1); //после этого действия obj1 станет Detached
	
obj1.setName("Kane"); //это изменение НЕ отобразится в БД
```
	
**Все объекты становятся Detached после закрытия сессии**
	
---
**Removed state**
	
Объект помечен для удаления и следующий `commit()` удалит соответствующую запись из БД. 
	
```java
entityManager.getTransaction().begin();
	
entityManager.remove(obj1); //объект obj1 помечен для удаления из БД
	
entityManager.getTransaction.commit(); //объект obj1 удалён из БД
```
<!--ID: 1769430384083-->



Q: Какое состояние у объекта `obj1` полсе выполнения этого кода?
```java
entityManager.persist(obj1);
```
A: Объект уже будет **persisted** -> вызывать `.merge()` для этого не надо. Однако, он ещё не будет сохранён в БД. Он туда попадёт только при вызове:
```java
entityManager.flush();
//или
entitiyManager.getTransaction().commit(); //внутри тоже вызывает flush()
```
<!--ID: 1769430384092-->
