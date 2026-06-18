
28-04-2026 19:05

Status:

Tags: [[Spring Batch]] [[Spring]]

---
# Contexts - Spring Batch


## Типы контекстов

```
JobExecution → ExecutionContext (JobContext)
↓
StepExecution → ExecutionContext (StepContext) 
↓ (внутри chunk)
ChunkContext → StepContext → JobContext
```


**3 уровня**:

1. **JobExecutionContext** — весь Job
    
2. **StepExecutionContext** — каждый Step
    
3. **ChunkContext** — текущий chunk (доступен в ItemReader/Processor/Writer)
    

## Срок жизни

|Контекст|Срок жизни|Область видимости|
|---|---|---|
|**JobContext**|До конца **JobExecution**|Все Steps|
|**StepContext**|До конца **StepExecution**|Только текущий Step|
|**ChunkContext**|Один **chunk** (1000 items)|Reader/Processor/Writer|

## Что хранится (типичные данные)

```
JobContext:
├── "total.count" = 15000          # Общее кол-во обработанных записей
├── "import.date" = "2026-04-28"   # Job параметры
└── "status" = "SUCCESS"

StepContext:  
├── "step1.read.count" = 10000     # Счётчики Step 1
├── "step1.errors" = 5             # Кол-во ошибок
└── "lastProcessedId" = 999        # Для restart

ChunkContext:
├── "chunk.id" = 5                 # Номер текущего chunk
└── "batch.size" = 1000            # Размер chunk
```


---
## Доступ к контекстам

#### 1. В ItemReader/Processor/Writer (ChunkContext)

```java
@Component
public class CompanyProcessor implements ItemProcessor<Company, Company> {
    
    @Override
    public Company process(Company item, ChunkContext chunkContext) {
        // StepContext
        StepContext stepContext = chunkContext.getStepContext();
        ExecutionContext stepCtx = stepContext.getStepExecution().getExecutionContext();
        
        // JobContext (только чтение!)
        Map<String, Object> jobCtx = stepContext.getJobExecutionContext();
        
        // Запись в StepContext (сохранится в БД!)
        stepCtx.put("processed.count", stepCtx.getInt("processed.count", 0) + 1);
        
        return item;
    }
}
```

#### 2. В Tasklet

```java
public class CleanupTasklet implements Tasklet {
    public RepeatStatus execute(StepContribution contribution, 
                              ChunkContext chunkContext) {
        
        StepExecution stepExecution = chunkContext.getStepContext().getStepExecution();
        ExecutionContext stepCtx = stepExecution.getExecutionContext();
        
        JobExecution jobExecution = stepExecution.getJobExecution();
        ExecutionContext jobCtx = jobExecution.getExecutionContext();
        
        // Чтение/запись
        stepCtx.put("cleanup.count", 1500);
        jobCtx.put("total.cleanup", 1500);
        
        return RepeatStatus.FINISHED;
    }
}
```


#### 3. Из StepListener (Job/StepExecutionListener)

```java
@Component
public class CompanyStepListener implements StepExecutionListener {
    
    @Override
    public void beforeStep(StepExecution stepExecution) {
        stepExecution.getExecutionContext().put("start.time", 
            LocalDateTime.now().toString());
    }
    
    @Override
    public ExitStatus afterStep(StepExecution stepExecution) {
        ExecutionContext ctx = stepExecution.getExecutionContext();
        log.info("Step processed {} items", ctx.getInt("processed.count", 0));
        return stepExecution.getExitStatus();
    }
}
```


---
## API для работы

```java
// ✅ Запись (StepContext)
stepCtx.put("key", value);           // Сохраняется в JobRepository
stepCtx.putInt("counter", 100);
stepCtx.putLong("bytes", 1024L);

// ✅ Чтение с дефолтом
int count = stepCtx.getInt("counter", 0);  // 0 если нет ключа
String date = stepCtx.getString("date");

// ❌ JobContext из StepContext — только чтение!
Map<String, Object> jobCtx = stepContext.getJobExecutionContext(); // UnmodifiableMap!
```


---
### Передача данных между Steps (ExecutionContextPromotionListener)

```java
@Bean
public StepExecutionListener promotionListener() {
    ExecutionContextPromotionListener listener = new ExecutionContextPromotionListener();
    listener.setKeys(new String[]{"processed.count", "total.errors"});  // Копировать в JobContext
    return listener;
}

@Bean
public Step firstStep() {
    return steps.get("firstStep")
        .<Company, Company>chunk(1000)
        .reader(reader)
        .writer(writer)
        .listener(promotionListener())  // Копирует в JobContext
        .build();
}
```

## Практические примеры

```java
// Step 1: Загрузка транзакций
stepCtx.put("total.transactions", 50000);
stepCtx.put("last.id", 123456);  // Для restart

// Step 2: Валидация (читает из JobContext)
int total = jobCtx.getInt("total.transactions", 0);
List<Long> invalidIds = stepCtx.get("invalid.ids", new ArrayList<>());
```

### Сохранение в БД (JobRepository)

```
BATCH_EXECUTION_CONTEXT:
├── JOB_EXECUTION_ID = 123
├── KEY = "total.count"
├── VALUE = "15000"
└── SHORT_CONTEXT = true
```

**Ключевые моменты**:

- ✅ **StepContext** = RW (пишется в БД)
    
- ✅ **JobContext из Step** = RO (UnmodifiableMap)
    
- ✅ **Данные сохраняются** в JobRepository автоматически
    
- ✅ **Restart** восстанавливает контекст из БД

----
#### [[Contexts - Spring Batch - Flashcards|Link to flashcards]]



---
### References:

