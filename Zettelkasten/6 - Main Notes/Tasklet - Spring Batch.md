
28-04-2026 08:37

Status:

Tags: [[Spring Batch]] [[Spring]]

---
# Tasklet - Spring Batch

Step должен иметь как минимум по одному Reader и Writer.

Но что делать, если нужно произвести одноразовую операцию: zip\unzip files, run a query, скачать файл из S3?

Для этого используется Tasklet:

```java
public class CleanUpTasklet implements Tasklet {
	
	@Overrride
	public RepeatStatus execute(
		StepContribution stepContribution, 
		ChunkContext chunkContext
	) throws Exception {
		FileUtils.cleanDirectory(new File("/user/trmpFiles"));
		
		stepContribution.setExitStatus(ExitStatus.COMPLETED);
		return RepeatStatus.FINISHED;
	}
}
```

> [!warning] Note
> `Tasklet` может находиться **в любом месте Job** — в начале, середине, конце или даже **чередоваться** с chunk-oriented Steps.

#### Использование:

```java
@Configuration
@EnableBatchProcessing
public class BatchConfig {
    
    private final JobBuilderFactory jobs;
    private final StepBuilderFactory steps;
    private final CleanupTasklet cleanupTasklet; // @Autowired
    
    public BatchConfig(JobBuilderFactory jobs, StepBuilderFactory steps, 
                      CleanupTasklet cleanupTasklet) {
        this.jobs = jobs;
        this.steps = steps;
        this.cleanupTasklet = cleanupTasklet;
    }
    
    @Bean
    public Step cleanupStep() {
        return steps.get("cleanupStep")
            .tasklet(cleanupTasklet)  // ← Custom Tasklet
            .build();
    }
    
    @Bean
    public Job cleanupJob() {
        return jobs.get("cleanupJob")
            .incrementer(new RunIdIncrementer()) // Для повторных запусков
            .start(cleanupStep())
            .build();
    }
}
```

----
#### [[Tasklet - Spring Batch - Flashcards|Link to flashcards]]



---
### References:

