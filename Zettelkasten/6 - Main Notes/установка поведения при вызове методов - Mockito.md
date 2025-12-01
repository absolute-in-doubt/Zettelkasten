
07-11-2025 14:50

Status: #baby 

Tags: [[Testing]] [[Java+]]

---
# установка поведения при вызове методов - Mockito

### Установка поведения

#### **Для void-методов** 

Так как `delete(employee)` возвращает `void`, обычный `when(...)` не подходит. Поэтому используется специальный синтаксис:

```java
doNothing().when(repository).delete(employee);
```

Здесь `doNothing()` возвращает объект (Stubber), который знает, как обрабатывать void-методы, и связывает его с вызовом `repository.delete(employee)`.


---
### Установка возвращаемого значения
### `OngoingStubbing<T>` ~={green}Mockito.when(`T methodCall`)=~

Возвращает объект, к которому нужно вызвать доп метод для установки возвращаемого значения.

```java
when(mock.someMethod(anyString())).thenAnswer( 
	new Answer() { 
		public Object answer(InvocationOnMock invocation) { 
			Object[] args = invocation.getArguments(); 
			Object mock = invocation.getMock(); 
			return "called with arguments: " + Arrays.toString(args); 
		} 
	}); 
//Following prints "called with arguments: [foo]" 
System.out.println(mock.someMethod("foo"));
```


### Методы применимые для `OngoingStubbing<T>`:

![[Pasted image 20251107145846.png]]

~={orange}Именно `OngoingStubbing<T>`, НЕ Mockito=~

```java
when(mock.someMethod()).thenReturn(10); 

//you can use flexible argument matchers, e.g: 
when(mock.someMethod(anyString())).thenReturn(10); 

//setting exception to be thrown: 
when(mock.someMethod("some arg")).thenThrow(new RuntimeException()); 

//you can set different behavior for consecutive method calls. 
//Last stubbing (e.g: thenReturn("foo")) determines the behavior of further consecutive calls. 
when(mock.someMethod("some arg")) .thenThrow(new RuntimeException()) .thenReturn("foo"); 

//Alternative, shorter version for consecutive stubbing: 
when(mock.someMethod("some arg")) .thenReturn("one", "two"); 

//is the same as: 
when(mock.someMethod("some arg")) .thenReturn("one") .thenReturn("two");

//shorter version for consecutive method calls throwing exceptions: 
when(mock.someMethod("some arg")) .thenThrow(new RuntimeException(), new NullPointerException();
```

----
#### [[установка поведения при вызове методов - Mockito - Flashcards|Link to flashcards]]



---
### References:

