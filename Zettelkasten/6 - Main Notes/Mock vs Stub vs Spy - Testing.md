
07-11-2025 14:29

Status: #adult 

Tags: [[Testing]] [[Java+]]

---
# Mock vs Stub vs Spy - Testing


### **Stub (заглушка)**

Возвращает заранее определённые значения, не проверяет взаимодействия.

```java
when(calculator.add(2, 3)).thenReturn(5);
```
   
### **Mock (мок)** 

Имитирует объект, позволяет задавать поведение и проверять вызовы методов.

```java
MyService service = mock(MyService.class);
when(service.process("data")).thenReturn("ok");
verify(service).process("data");
```

### **Spy (шпион)** 


Оборачивает реальный объект: по умолчанию вызывает настоящие методы, но отдельные можно переопределить.

```java
List<String> list = new ArrayList<>();
List<String> spyList = spy(list);

spyList.add("A"); // вызовет реальный метод add
when(spyList.size()).thenReturn(100); // переопределили метод size
```

Таким образом, часть логики работает «по-настоящему», а часть — под контролем теста.

----
#### [[Mock vs Stub vs Spy - Testing - Flashcards|Link to flashcards]]



---
### References:

