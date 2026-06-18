
07-11-2025 11:27

Status: #baby 

Tags: [[Testing]] [[Java+]]

---
# startup - Mockito

Добавляется как расширение JUnit.

Для этого у JUnit есть специальная аннотация @ExtendWith():
```java
@ExtendWith(MockitoExtension.class)
```

> [!warning] 
> Mock-аем только свои интерфейсы, third party interfaces need to be used as they are. Потому что со временем они могут измениться -> тесты будут выполняться, а прога - не работает.


### Создание Mock-объекта

~={purple}@Mock=~ - ставится на объекте "болванке".

~={purple}@InjectMocks=~ - ставится на объекте, который владеет этим mock-ом

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
#### [[startup - Mockito - Flashcards|Link to flashcards]]



---
### References:

