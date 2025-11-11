
Theory for the cards: 

FILE TAGS: hibernate jpa

Q: В чём разница между методом save из Hibernate и методом persist из JPA?
A: Метод save(...) сразу генерирует id объекта и возвращает его:
```java
int id = session.save(new Person("Jake", 18));
```
	
Метод persist(...) определён в JPA и также реализуется Hibernate. Однако он не возвращает ничего и загрузка в память данных, сгенерированных бд (т.е. id) происходит ==lazily==:
```java
Person person = new Person("Jake", 18);
session.persist(person);
// или 
entityManager.persist(person);
	
int id = person.getId(); //если не вызвать геттер в рамках транзакции, то после её завершения id так и не будет загруужено в память
```
<!--ID: 1761734029806-->
