
28-04-2026 08:56

Status:

Tags: [[Spring Batch]] [[Spring]]

---
# Listeners - Spring Batch


![[Pasted image 20260428085659.png]]


### Use cases

- at the end of your job you need to send an email notification: "Job complaeted", "Job failed"


### Creating listeners:

```java
public class SomeListener{
	
	@BeforeJob
	public void beforeJob(JobExceution jobExceution) {}
	
	@AfterJob
	public void afterJob(JobExecution jobExecution) {}`
}
```



```java
@Bean
public Job job(Step step) {
    return jobs.get("job")
		    .listener(new SomeListener())
            .start(step)
            .build();
}
```

----
#### [[Listeners - Spring Batch - Flashcards|Link to flashcards]]



---
### References:

