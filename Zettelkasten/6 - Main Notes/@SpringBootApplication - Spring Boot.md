
03-11-2025 12:42

Status: #baby 

Tags: [[Spring Boot]] [[Spring]]

---
## `@SpringBootApplication`


![[Pasted image 20260110174320.png]]

Содержит в себе:
- ~={purple}@SpringBootConfiguration=~  
- ~={purple}@EnableAutoConfiguration=~
- ~={purple}@ComponentScan=~ - настроен так, что рекурсивно  сканирует классы в текущей папке и подпапках:
![[Pasted image 20251109093940.png]]
Отсканирует папку `org.bsuir.spring.boot.app` (Где находится только сам Application) + подпапки (controller, service (и его подпапки))

> [!warning] 
> Класс с аннотацией ~={purple}@SpringBootApplication=~ должен находиться в корне проекта.
> Или придётся вручную настраивать сканирование пакетов.


---
### Настройка автоконфигурации

При помощи `exclude` property в аннотации можно исключить ненужные автоконфигурации подключаемых стартеров:

```java
@SpringBootApplication(
    excludeName = {
        "com.example.FirstAutoConfiguration",
        "com.example.SecondAutoConfiguration"
    }
)
```

Хотя чище это делать через yaml файл:

```yaml
spring:
  autoconfigure:
    exclude:
      - com.mycustomstarter.configurations.FirstAutoConfiguration
      - com.mycustomstarter.configurations.SecondAutoConfiguration
```


----
#### [[setup - Spring Boot - Flashcards|Link to flashcards]]



---
### References:

