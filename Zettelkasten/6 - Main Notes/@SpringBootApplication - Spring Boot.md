
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

----
#### [[setup - Spring Boot - Flashcards|Link to flashcards]]



---
### References:

