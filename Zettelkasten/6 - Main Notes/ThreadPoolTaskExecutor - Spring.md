
24-11-2025 19:26

Status:

Tags: [[Spring]]

---
# ThreadPoolTaskExecutor - Spring

- Это основной бин для пула потоков в Spring:

```java
@Bean 
public ThreadPoolTaskExecutor parsingExecutor() {     
	ThreadPoolTaskExecutor exec = new ThreadPoolTaskExecutor();     
	exec.setCorePoolSize(4);     
	exec.setMaxPoolSize(8);     
	exec.setQueueCapacity(100);     
	exec.setThreadNamePrefix("parser-");     
	exec.initialize();     
	return exec; 
}
```

- **Использовать:**
    
    - через ~={yellow}@Async("parsingExecutor")=~
    
    - или вручную: ~={yellow}parsingExecutor.submit(...)=~
    
- **Если нужна планировка задач (cron, задержки):**  
    Используется `ThreadPoolTaskScheduler`:
    
```java
@Bean 
public ThreadPoolTaskScheduler scheduler() {
	ThreadPoolTaskScheduler s = new ThreadPoolTaskScheduler();
	s.setPoolSize(4);     
	s.setThreadNamePrefix("scheduler-");     
	return s; 
  }
```
    
- **Если уже используется Spring Boot:**
    
    - `TaskExecutorBuilder` и `TaskSchedulingBuilder` позволяют конфигурировать executors через application.yml.
	
    - Но это те же ThreadPoolTaskExecutor/TaskScheduler под капотом.
	
- **Рекомендации:**
    
    - Для разных типов задач — свой executor (например, parsingExecutor, ioExecutor).
	
    - Не использовать `newFixedThreadPool`, `newCachedThreadPool` — Spring-бины управляются лучше.
	
    - Не делать слишком большой пул — ориентируйся на тип нагрузки (CPU bound → мало потоков, IO bound → больше).

----
#### [[ThreadPoolTaskExecutor - Spring - Flashcards|Link to flashcards]]



---
### References:

