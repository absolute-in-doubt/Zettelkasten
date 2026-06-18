
07-11-2025 14:46

Status: #not_even_born 

Tags: [[Testing]] [[Java+]]

---
# Создание mock - Mockito


### Без **~={purple}@ExtendWith(`MockitoExtension.class`)=~**:

```java
class ProducControllerTest{
	
	private ProductController mockController = Mockito.mock(ProductController.class);
	
	...
	
}
```


### С **~={purple}@ExtendWith(`MockitoEXtension.class`)=~**

```java
@ExtendWith(MockitoExtension.class)
class ProducControllerTest{
	
	@Mock
	private ProductController mockController;
	
	...
	
}
```

##### Инъекция mock-ов через аннотацию

`+` ~={purple}@InjectMocks=~ - ставится на объекте, который владеет этим mock-ом

```java
@ExtendWith(MockitoExtension.class)  
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)  
public class EmployeeRepositoryTest {  
    @InjectMocks  
    private EmployeeService service;  
    @Mock  
    private EmployeeRepository repository;  
    private static Employee employee;
```



----
#### [[Создание mock - Mockito - Flashcards|Link to flashcards]]



---
### References:

