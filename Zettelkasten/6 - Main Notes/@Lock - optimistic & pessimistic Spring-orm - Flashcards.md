
Theory for the cards: [[@Lock - optimistic & pessimistic Spring-orm]]

FILE TAGS: spring

Q: Как установить pessimistic/optimistic blocks в Spring Data?
A: Использзуя аннотацию @Lock:
	
```java
interface MyEntityRepository extends JpaRepository<Entity, Integer>{
	
	@Lock(LockModeType.PESSIMISTIC_WRITE)
	public void find(Integer id);	
} 
```
<!--ID: 1769523691150-->
