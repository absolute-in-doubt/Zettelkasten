
01-05-2026 12:32

Status:

Tags: [[Spring Batch]] [[Spring]]

---
# @StepScope - Spring Batch


## Назначение

**`@StepScope`** — аннотация для **late binding** ~={red}(отложенное создание bean'ов) **в контексте каждого Step Execution**.=~

**Проблема без @StepScope**:

```java
@Bean  // Singleton — создаётся ОДИН раз при старте приложения
public ItemReader reader(@Value("#{jobParameters['fileName']}") String fileName) {
    // fileName = null! Параметры ещё недоступны
}
```

**С @StepScope**:

```java
@Bean
@StepScope  // Создаётся для КАЖДОГО StepExecution
public ItemReader reader(@Value("#{jobParameters['fileName']}") String fileName) {
    // fileName = "data.csv" — late binding!
}
```

## Где ставится

```j
✅ @Bean ItemReader/Writer/Processor
✅ @Bean Tasklet  
✅ StepExecutionListener
✅ ItemStream (ResourceAwareItemReader/Writer)
✅ Любые компоненты Step'а
```

```
❌ НЕ ставить на:
- Job beans (используйте @JobScope)
- Конфигурационные классы
- Сервисы вне Step
```



---
## Основные use cases

#### 1. **JobParameters / StepContext**

```java
@Bean @StepScope
public FlatFileItemReader csvReader(
    @Value("#{jobParameters['fileName']}") String fileName) {
    return new FlatFileItemReaderBuilder()
        .resource(new ClassPathResource(fileName))  // ✅ Динамический файл
        .build();
}
```

#### 2. **ExecutionContext**


```java
@Bean @StepScope
public ItemReader reader(
    @Value("#{stepExecutionContext['offset']}") Integer offset) {
    return new DatabaseItemReader(offset);  // ✅ Restart с offset
}
```


#### 3. **Динамические ресурсы**

```java
@Bean @StepScope
public ItemWriter writer(
    @Value("#{stepExecutionContext['outputPath']}") String path) {
    return new FlatFileItemWriterBuilder()
        .resource(new FileSystemResource(path))
        .build();
}
```


---
## Срок жизни

```
Application start
   ↓
Job start → Step 1 Execution → @StepScope bean #1 (file="data1.csv")
              ↓
            Step 1 END
              ↓
            Step 2 Execution → @StepScope bean #2 (file="data2.csv")
              ↓
            Job END → destroy всех @StepScope beans
```

**Каждый StepExecution** = **новый экземпляр** `@StepScope` bean'а.


---
### Late Binding SpEL

```
#{jobParameters['key']}           # Job параметры
#{stepExecutionContext['key']}    # Step контекст
#{jobExecutionContext['key']}     # Job контекст (read-only)
#{stepExecution.stepName}         # Имя текущего Step
```

## Обязательность

```
✅ **ОБЯЗАТЕЛЬНО** для:
- ItemReader/Writer с JobParameters
- Restartable Readers (offset из контекста)
- Динамические ресурсы (пути, URL)

✅ **РЕКОМЕНДУЕТСЯ** для:
- Tasklet с параметрами
- SkipListener, ItemReadListener

❌ **НЕ НУЖНО** для:
- Stateless Processor'ы
- JPA репозитории
```



----
#### [[@StepScope - Spring Batch - Flashcards|Link to flashcards]]



---
### References:

