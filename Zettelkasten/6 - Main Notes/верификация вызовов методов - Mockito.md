
07-11-2025 14:59

Status: #baby

Tags: [[Testing]] [[Java+]]

---
# верификация вызовов методов - Mockito

#### Mock static methods:

~={green}verify(`T mock`)=~ - верификация, что определённое поведение произошло один раз

~={green}verify(`T mock, VerificationMode mode`) =~- верификация, что определенное поведение произошло ни разу / определённое число раз:

```java
verify(mock, times(5)).someMethod("was called five times"); 

verify(mock, never()).someMethod("was never called"); 

verify(mock, atLeastOnce()).someMethod("was called at least once");

verify(mock, atLeast(2)).someMethod("was called at least twice"); 

verify(mock, atMost(3)).someMethod("was called at most 3 times");
```


~={green}verifyNoInteractions(`Object ...mocks`)=~ - верифицирует, что с этими моками не было никаких взаимодействий




### Верификация порядка вызова методов

```java
// A. Single mock whose methods must be invoked in a particular order 
List singleMock = mock(List.class); 

//using a single mock 
singleMock.add("was added first"); 
singleMock.add("was added second"); 

//create an inOrder verifier for a single mock 
InOrder inOrder = inOrder(singleMock); 

//following will make sure that add is first called with "was added first", then with "was added second" 

inOrder.verify(singleMock).add("was added first"); 
inOrder.verify(singleMock).add("was added second"); 

// B. Multiple mocks that must be used in a particular order List 
firstMock = mock(List.class); 
List secondMock = mock(List.class); 

//using mocks 
firstMock.add("was called first"); 
secondMock.add("was called second"); 

//create inOrder object passing any mocks that need to be verified in order 
InOrder inOrder = inOrder(firstMock, secondMock); 

//following will make sure that firstMock was called before secondMock 
inOrder.verify(firstMock).add("was called first"); 
inOrder.verify(secondMock).add("was called second"); 

// Oh, and A + B can be mixed together at will
```


----
#### [[верификация вызовов методов - Mockito - Flashcards|Link to flashcards]]



---
### References:

