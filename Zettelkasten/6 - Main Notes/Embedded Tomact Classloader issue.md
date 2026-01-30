
02-11-2025 10:58

Status:

Tags: [[Tomcat]] [[Java+]]

---
# Embedded Tomact Classloader issue


Обычно все ClassLoaders are Parent-first by default. -> Сначала делегирубт загрузку класса родительским ClassLoader-ам, а сами загружают только, если очередь доходит до них.

Но у Embedded Tomcat Classloader - child-first.

И если один и тот же класс загружетмся разными ClassLoader- ами, то он считается НЕ одним и тем же типом. => Получаем ClassCastException на ровном месте:

```java
java.lang.ClassCastException: class org.bsuir.spring.data.crud.model.Person cannot be cast to class org.bsuir.spring.data.crud.model.Person (org.bsuir.spring.data.crud.model.Person is in unnamed module of loader 'app'; org.bsuir.spring.data.crud.model.Person is in unnamed module of loader org.apache.catalina.loader.ParallelWebappClassLoader @2b4bac49)
```

### Решение:

Сделать CLassLoader Tomcat тоже Parent-first. Тогда загрузка класса будет делегирована наверх -> User-defined классы будут загружены System Classloader-ом:

```java
WebappLoader webappLoader = new WebappLoader();
webappLoader.setDelegate(true);
context.setLoader(webappLoader);
```

----
#### [[Embedded Tomact Classloader issue - Flashcards|Link to flashcards]]



---
### References:

- [[ClassLoader - JMM]]