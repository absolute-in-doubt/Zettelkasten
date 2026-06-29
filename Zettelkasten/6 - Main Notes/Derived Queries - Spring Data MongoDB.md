
27-06-2026 17:01

Status:

Tags: [[Spring Data MongoDB]] [[Spring Data Access]] [[Spring]]

---
# Derived Queries - Spring Data MongoDB


```java
interface PersonRepository extends Repository<Person, Long> {

List<Person> findByEmailAddressAndLastname(
		  EmailAddress emailAddress, 
		  String lastname
	  );
	
 // Enables the distinct flag for the query
 List<Person> findDistinctPeopleByLastnameOrFirstname(
		  String lastname, 
		  String firstname
	  );
 List<Person> findPeopleDistinctByLastnameOrFirstname(
	  String lastname, 
	  String firstname
  );
	
// Enabling ignoring case for an individual property
 List<Person> findByLastnameIgnoreCase(
		  String lastname
	  );
	  
// Enabling ignoring case for all suitable properties
List<Person> findByLastnameAndFirstnameAllIgnoreCase(
	String lastname, 
	String firstname
);

  // Enabling static ORDER BY for a query
  List<Person> findByLastnameOrderByFirstnameAsc(String lastname);
  List<Person> findByLastnameOrderByFirstnameDesc(String lastname);
}
```


----
#### [[Derived Queries - Spring Data MongoDB - Flashcards|Link to flashcards]]



---
### References:

https://docs.spring.io/spring-data/mongodb/reference/repositories/query-methods-details.html#repositories.query-methods.query-creation