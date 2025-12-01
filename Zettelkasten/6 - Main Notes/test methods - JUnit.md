 
06-11-2025 10:15

Status:

Tags: [[Testing]] [[Java+]] 

---
# test methods - JUnit

Каждый Test-иетод аннотируется @Test или @ParametrizedTest.

~={purple}@ParametrizedTest=~ - позволяет проводить один и тот же тест несколько раз с разными параметрами. Параметры могут поставляться различными аннотациями извне: ~={purple}@FieldSource=~, ~={purple}@ValueSource=~ и т.д. Подробнее [[Параметризованные тесты - JUnit|тут]]


### Методы проверки

[Все Assertions методы](https://docs.junit.org/5.0.1/api/org/junit/jupiter/api/Assertions.html)

- ~={green}assertEquals(`T expected, T actual`)=~
- ~={green}assertEquals(`double expected, double actual, double delta`)=~ - для сравнения дробных используем допустимую дельту, т.к. floating point variables имеют погрешность рассчётов.

- ~={green}assertThrows(`Class<T> expectedException, Executable esecutable`) =~ 
	Пример: `assertThrows(MyException.class, () -> myMethod());`


### Вспомогательные методы

Для выполнения тестов иногда нужно аллоцировать память или выполнять какие-то другие приготовления, а после них - очистку.

Для этого используются аннотации:

- ~={purple}@BeforeEach=~ - указывает на то, что метод будет выполнятся перед каждым тестируемым методом

 - ~={purple}@AfterEach=~ - указывает на то что метод будет выполнятся после каждого тестируемого метода 

- ~={purple}@BeforeAll=~ - выполняется только один раз перед запуском тестов  ==**has to be static**==

- ~={purple}@AfterAll=~ - выполняется один раз после всех тестов  ==**has to be static**==


### Порядок выполнения тестов

По умолчанию, у тестов есть свой определенный, но неочевидный порядок исполнения. Благодаря аннотации ~={purple}@TestMethodOrder=~ мы можем настроить порядок выполнения тестов с помощью аннотации ~={purple}@Order=~.

```java
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)  
class EmployeeDAOTest {  
    @Test  
    @Order(1)  
    void createValidEmployeeTest() {  
		//some testing logic 
	}
        
    @Test  
	@Order(2)  
	void createInvalidEmployeeTest() {  
		//some testing order
    }  
}
```


### Исключение тестовых методов (или классов)

Для этого есть аннотация ~={purple}@Disabled=~:

```java
@Disabled("Пока не реализована логика сохранения")
@Test
void saveEmployeeTest() {
    // этот тест не будет выполняться
}
```

```java
@Disabled("Весь класс временно отключён")
class EmployeeDaoTest {
    @Test
    void testFindEmployee() { ... }
}
```

----
#### [[test methods - JUnit - Flashcards|Link to flashcards]]



---
### References:

