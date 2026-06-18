
29-04-2026 11:35

Status:

Tags: [[Spring Batch]] [[Spring]]

---
# Запуск Job - Spring Batch



### Запуск через JobLauncher

```java
@SpringBootApplication
public class Main implements CommandLineRunner {
    @Autowired
    private Job job;
    @Autowired
    private JobLauncher jobLauncher;
    @Value("${file.input}")
    private String input;

    @Value("${file.output}")
    private String output;

    public static void main(String[] args) {
        SpringApplication.run(Main.class, args);

    }

    @Override
    public void run(String... args) throws Exception {
        JobParametersBuilder jobParameters = new JobParametersBuilder();
        jobParameters.addString("file.input", input);
        jobParameters.addString("file.output", output);
        jobParameters.addString("trial","1");

        jobLauncher.run(job, jobParameters.toJobParameters());
    }
}
```

> [!warning]
> Если повторно запустить тот же Batch Job без изменеия jobParameters, сделано ничего не будет, вместо этого выбросится исключение:
> ```
> Caused by: org.springframework.batch.core.repository.JobInstanceAlreadyCompleteException: A job instance already exists and is complete for parameters={file.input=src/main/resources/input.csv, file.output=src/main/resources/output.json, trial=1}.  If you want to run this job again, change the parameters.
> ```
> **Потому что для данного _JobInstance_ успешный _JobExecution_ (то есть _JobExecution_ со статусом _COMPLETED_) может быть только один.**


---
### Полноценный Scheduler service

```java
@Service
@Slf4j
@EnableScheduling  // Включаем планировщик
public class BatchJobSchedulerService {
    
    private final JobLauncher jobLauncher;
    private final Job companyImportJob;
    private final Job cleanupJob;
    private final Job dailyReportJob;
    
    public BatchJobSchedulerService(JobLauncher jobLauncher,
                                  @Qualifier("companyImportJob") Job companyImportJob,
                                  @Qualifier("cleanupJob") Job cleanupJob,
                                  @Qualifier("dailyReportJob") Job dailyReportJob) {
        this.jobLauncher = jobLauncher;
        this.companyImportJob = companyImportJob;
        this.cleanupJob = cleanupJob;
        this.dailyReportJob = dailyReportJob;
    }
    
    // 🔥 1. Ежедневный импорт компаний (2:00 AM)
    @Scheduled(cron = "0 0 2 * * ?")
    public void scheduleDailyCompanyImport() {
        runJob(companyImportJob, Map.of("importDate", LocalDate.now().toString()));
    }
    
    // 🔥 2. Недельная очистка (воскресенье 1:00 AM)
    @Scheduled(cron = "0 0 1 ? * SUN")
    public void scheduleWeeklyCleanup() {
        runJob(cleanupJob, Map.of("retentionDays", "30"));
    }
    
    // 🔥 3. Почасовые отчёты (каждый час)
    @Scheduled(fixedRateString = "3600000")  // 1 час = 3600000ms
    public void scheduleHourlyReports() {
        runJob(dailyReportJob, Map.of("reportHour", LocalTime.now().getHour()));
    }
    
    // 🔥 4. Ручной запуск через REST (для операторов)
    @Async  // Асинхронно, не блокирует REST
    public CompletableFuture<JobExecution> runCompanyImportAsync(String dateFrom) {
        return CompletableFuture.supplyAsync(() -> {
            try {
                return runJob(companyImportJob, Map.of("dateFrom", dateFrom));
            } catch (Exception e) {
                log.error("Failed to run company import", e);
                throw new RuntimeException(e);
            }
        });
    }
    
    // 🔥 Универсальный метод запуска
    private JobExecution runJob(Job job, Map<String, String> params) {
        try {
            JobParameters jobParameters = new JobParametersBuilder()
                .addLong("launchTime", System.currentTimeMillis())  // Обязательно уникально!
                .addString("username", getCurrentUser())             // Аудит
                .addParameters(params.entrySet().stream()
                    .collect(Collectors.toMap(Map.Entry::getKey, 
                                            e -> new JobParameter(e.getValue()))))
                .toJobParameters();
                
            JobExecution execution = jobLauncher.run(job, jobParameters);
            log.info("Started Job: {} | Status: {} | ID: {}", 
                    job.getName(), execution.getStatus(), execution.getId());
            return execution;
            
        } catch (JobInstanceAlreadyCompleteException e) {
            log.warn("Job {} already completed for parameters: {}", job.getName(), params);
            return null;
        } catch (JobParametersInvalidException | JobExecutionAlreadyRunningException e) {
            log.error("Cannot start Job {}: {}", job.getName(), e.getMessage());
            throw new RuntimeException(e);
        }
    }
    
    private String getCurrentUser() {
        return SecurityContextHolder.getContext()
            .getAuthentication()
            .getName();  // Spring Security
    }
}
```

**P.S.** `JobLauncher` - Deprecated -> **Замена: `JobOperator`** (extends `JobLauncher` + дополнительные возможности).

---
#### JobOperator


> [!warning] Note
> **`JobOperator` сам находит Job по названию** через **JobRegistry**. **НЕ нужно передавать Job bean** — только **String имя**.

###### **Авторегистрация в Spring Boot**

**Spring Boot AutoConfiguration** (`BatchAutoConfiguration`) **автоматически**:

1. ✅ Создаёт **`MapJobRegistry`** (`SmartInitializingSingleton`)
    
2. ✅ **Регистрирует** все `@Bean Job` по их **имени**
    
3. ✅ Создаёт **`JobOperator`**
    
4. ✅ Регистрирует **`JobExplorer`**

```java
@Service  
public class RequestTickersBatchService {  
  
    private final JobOperator jobOperator;  
    private final Job requestTickersJob;  
  
  
    public RequestTickersBatchService(JobOperator jobOperator,  
                                      Job requestTickersJob) {  
        this.jobOperator = jobOperator;  
        this.requestTickersJob = requestTickersJob;  
  
    }  
  
    @Async  
    public void requestTickers() throws JobInstanceAlreadyCompleteException, InvalidJobParametersException, JobExecutionAlreadyRunningException, JobRestartException {  
        JobExecution jobExecution = jobOperator.start(requestTickersJob, new JobParametersBuilder()  
                .addLong("time", System.currentTimeMillis())  
                .toJobParameters());  
  
    }  
}
```



----
#### [[Запуск Job - Spring Batch - Flashcards|Link to flashcards]]



---
### References:

