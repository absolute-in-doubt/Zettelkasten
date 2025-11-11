
04-11-2025 12:56

Status: #adult 

Tags: [[JAXB]]

---
# Marshaller class - JAXB

[Link to JavaDoc](https://docs.oracle.com/javase/8/docs/api/javax/xml/bind/Marshaller.html)

**Marshaller** – это компонент JAXB, который выполняет маршалинг, преобразуя объекты Java в XML-документ в соответствии с настройками и аннотациями класса.

```java
JAXBContext context = JAXBContext.newInstance(Department.class);  
Marshaller m = context.createMarshaller();  
m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, Boolean.TRUE); //– задаёт свойство для объекта Marshaller, чтобы вывод XML был отформатирован (с отступами и переносами строк), делая его более читаемым для человека 
m.marshal(dept, System.out);  
File outFile = new File(XML_FILE);  
m.marshal(dept, outFile);  
System.err.println("Write to file: " + outFile.getAbsolutePath());
```


---
### Методы:

- ~={green}.setProperty(`String proopertyName, Object value`) =~- для настройки маршаллера. Подробнее о доступных properties - [check JavaDoc](https://docs.oracle.com/javase/8/docs/api/javax/xml/bind/Marshaller.html)

- ~={green}.marshal(`Object jaxbElement, File output`)=~
- ~={green}.marshal(`Object jaxbElement, OutputStream os`)=~ - маршаллит левый параметр и передаёт результат в правый

----
#### [[Marshaller class - JAXB - Flashcards|Link to flashcards]]



---
### References:

- [[JAXBContext class - JAXB]]