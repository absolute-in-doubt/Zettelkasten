
11-01-2026 10:35

Status:

Tags: [[SLF4J]] [[Logging]] [[Java+]]

---
# Как библиотеки выбирают реализацию логгирования - SLF4J


#### `spring-jcl` 

Форк Apache Commons Logging (~={blue}тоже фасад, на есть свой fallback=~). Spring‑JCL **заменяет** `commons-loggin.jar` и выполняет роль фасада, который перенаправляет логирование в современные backend’ы (Log4j API, SLF4J, JUL)

1. если доступен Log4j2 API (классы из `log4j-api` и, желательно, `log4j-core`), создаётся обёртка, которая делегирует логирование в Log4j2;

2. если Log4j2 нет, но есть SLF4J, используется SLF4J;
   
3. иначе — fallback на `java.util.logging`.​


#### `org.jboss.logging` (тоже фасад)

1.  JBoss LogManager (в среде WildFly / EAP) - специальный логгер для серверов семейства JBoss/WildFly;
    
2. **Log4j 2**
    
3. **SLF4J + Logback**
    
4. **Log4j 1.x** (устаревший)
    
5. **Java Util Logging (JUL)**



---
### References:

