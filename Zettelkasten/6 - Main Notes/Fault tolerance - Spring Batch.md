
30-04-2026 13:43

Status:

Tags: [[Spring Batch]] [[Spring]]

---
# Fault tolerance - Spring Batch


**Fault Tolerance** — механизмы обработки ошибок, позволяющие **продолжить выполнение** при сбоях отдельных элементов, а не **останавливать весь Job**.

#### Основные механизмы

```
1. SKIP (пропуск) — игнорировать ошибочные элементы
2. RETRY (повтор) — повторить неудачный chunk
3. FAULT_TOLERANT — включить отказоустойчивость
4. SkipPolicy / RetryPolicy — кастомная логика
```

---
### Настройка в Step (chunk-oriented)

```java
@Bean
public Step faultTolerantStep() {
    return steps.get("faultTolerantStep")
        .<Company, Company>chunk(1000)
        .reader(apiReader)
        .processor(companyProcessor)
        .writer(dbWriter)
        .faultTolerant()  // ← Включаем fault tolerance
        // SKIP: пропускаем ошибки
        .skipLimit(1000)           // Макс. 1000 пропусков на Step
        .skip(ValidationException.class)  // Пропускаем валидационные
        .skip(DataFormatException.class)
        .noSkip(FatalException.class)     // НИКОГДА не пропускать
        // RETRY: повторяем неудачные chunks
        .retryLimit(3)                 // Макс. 3 повтора на chunk
        .retryLimit(TransientDataAccessException.class)  // Временные БД ошибки
        .retry(SocketTimeoutException.class)
        .noRetry(DeadlockLoserDataAccessException.class) // Дедлок — фатально
        .backOffPolicy(backoff())      // Задержка между retry
        .build();
}
```

#### Поведение ошибок

|Ошибка|SKIP|RETRY|Результат|
|---|---|---|---|
|**ValidationException**|✅|❌|**Пропуск item** → chunk продолжается|
|**TransientDataAccessException**|❌|✅ (3 раза)|**Retry chunk** → успех/FAIL chunk|
|**FatalException**|❌|❌|**FAIL chunk** → FAIL Step → FAIL Job|
|**skipLimit(1000)** превышен|❌|❌|**FAIL Step** → FAIL Job|


----
### Retry Backoff (экспоненциальная задержка)

```java
@Bean
public BackOffPolicy backoff() {
    ExponentialBackOffPolicy policy = new ExponentialBackOffPolicy();
    policy.setInitialInterval(1000);   // 1 сек
    policy.setMultiplier(2.0);         // ×2
    policy.setMaxInterval(30000);      // Макс. 30 сек
    return policy;
}
// Retry: 1s → 2s → 4s → 8s → 16s → FAIL
```


---
### SkipPolicy (кастомная логика пропуска)

```java
@Component
public class CustomSkipPolicy implements SkipPolicy {
    private final int maxErrors = 1000;
    
    @Override
    public boolean shouldSkip(Throwable t, int skipCount) {
        return skipCount < maxErrors && 
               !(t instanceof FatalException);
    }
}
```


---
### Chunk vs Job failure

```
❌ Chunk FAIL (без faultTolerant):
Reader/Processor/Writer → Exception
→ rollback ТОЛЬКО текущий chunk
→ FAIL Step → FAIL Job

✅ Chunk FAIL (с faultTolerant):
SKIP: проблемный item → пропуск → chunk OK
RETRY: chunk повторяется → успех/FAIL chunk
FAIL chunk → FAIL Step → FAIL Job (если skipLimit превышен)
```


---
## Полный пример 


```java
@Bean
public Step companyImportStep() {
    return steps.get("companyImportStep")
        .<Company, Company>chunk(1000)
        .reader(apiReader)
        .processor(validator)  // Может кинуть ValidationException
        .writer(jpaWriter)
        .faultTolerant()
        // Пропускаем невалидные компании
        .skipLimit(5000)
        .skip(CompanyValidationException.class)
        .skip(DataFormatException.class)
        .noSkip(DatabaseConnectionException.class)
        // Retry при сетевых/БД проблемах
        .retryLimit(3)
        .retry(SocketTimeoutException.class)
        .retry(TransientDataAccessException.class)
        .backOffPolicy(exponentialBackOff())
        // Логирование
        .listener(skipListener())
        .listener(retryListener())
        .build();
}

```
#### SkipListener / RetryListener


```java
@Component
public class CompanySkipListener implements SkipListener<Company, Company> {
    @Override
    public void onSkipInRead(Company item, Throwable t) {
        log.warn("Skipped invalid company: {} | Reason: {}", item, t.getMessage());
    }
    
    @Override
    public void onSkipInWrite(Company item, Throwable t) {
        log.error("Failed to write company: {}", item.getId());
    }
}
```

### Job vs Step failure

```
Step FAIL → Job FAIL (по умолчанию)

Job-level retry:
@Bean
public Job companyJob() {
    return jobs.get("companyJob")
        .listener(jobRetryListener())
        .start(importStep())
        .build();
}
```


----

**Ключевые моменты**:

- **`.faultTolerant()`** — включает skip/retry
    
- **SKIP** = продолжить chunk без проблемного item
    
- **RETRY** = повторить весь chunk
    
- **FAIL chunk** → FAIL Step → FAIL Job
    
- **SkipLimit** превышен → FAIL Step → FAIL Job
    



----
#### [[Fault tolerance - Spring Batch - Flashcards|Link to flashcards]]



---
### References:

