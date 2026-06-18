
18-04-2026 11:31

Status:

Tags: [[Spring Batch]] [[Spring]]

---
# Spring Batch overview


![[Pasted image 20260418113108.png]]


- **Job Launcher** - запускает Job

- **Tasklet** - отвечает за выполнение работы

- **JobRepository** - хранит в служебных таблицах БД задания _Job_, попытки их выполнения _JobExecution_ и многое другое — всего 6 таблиц.


> [!note]
> Spring Boot создает служебные таблицы Spring Batch автоматически, если в настройках указать:
> ```properties
> spring.batch.initialize-schema=always
> ```


---
### ~={purple}@EnableBatchProcessing=~

Автоконфигурирует:
1. JobRepository
	
2. JobLauncher
	
3. JobRegistry
	
4. JobExplorer
	
5. JobOperator

> [!warning]
> `@EnableBatchProcessing` автоконфигурировала эти объекты в Spring Batch < 5 версии. В 5 версии сделали какую-то хуйню и при добавлении `@EnableBatchProcessing` эти бины не автоконфигурируются, зато без неё все автоконфигурируется. 
> 
> Понабирают долбоебов крч, по боъявлению)


----
#### [[Spring Batch overview - Flashcards|Link to flashcards]]



---
### References:

