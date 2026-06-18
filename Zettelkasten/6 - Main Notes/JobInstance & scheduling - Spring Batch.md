
18-04-2026 13:01

Status:

Tags: [[Spring Batch]] [[Spring]]

---
# JobInstance & scheduling - Spring Batch

Один и тот же Job нельзя запускать несколько раз.

 Если повторно запустить Job, сделано ничего не будет, вместо этого выбросится исключение:

```
Caused by: org.springframework.batch.core.repository.JobInstanceAlreadyCompleteException: A job instance already exists and is complete for parameters={file.input=src/main/resources/input.csv, file.output=src/main/resources/output.json, trial=1}. If you want to run this job again, change the parameters.
```

**Потому что для данного _JobInstance_ успешный _JobExecution_ (то есть _JobExecution_ со статусом _COMPLETED_) может быть только один.**

---
### JobInstance

Job - только описание работы, для фактического выполнения создаётся JobInstance:

![[Pasted image 20260418130308.png]]

Часто (например, при запуске задания по расписанию с _@Scheduled_)  добавляют автоматически генерируемый уникальный параметр:

```java
.addLong("launchTime", System.currentTimeMillis())
```

~={pink}Может ли для одного _JobInstance_ быть несколько _JobExecution_?=~
	Да: успешный _JobExecution_ (со статусом _COMPLETED_) должен быть единственным, но со статусом  _FAILED_ их может быть много. Если бы при предыдущем запуске возникло исключение и задание не выполнилось, результат _JobExecution_ был бы _FAILED_. Тогда были бы разрешены новые попытки выполнения задания, и каждый раз в таблицу добавлялся бы новый _JobExecution_ для старого _JobInstance_ (до тех пор, пока не получится _COMPLETED)_. Эти попытки называются рестартом задания — и это как раз та самая уникальная возможность Spring Batch, позволяющая повторить задание с проблемной точки, а не с нуля.


----
### Пример рестарта Job


```java
@Bean
public Job job(Step step) {
    return jobs.get("job")
            .start(step)
            .build();
}
```

Чтобы вызвать исключение, испортим исходный файл, чтобы при парсинге 5 строки возникло исключение:

```csv
1,dog
2,cat
3,fox
4,elephant
,,,5,eagle
6,squirrel
7,tiger
8,shark
```

В таблице появится еще один _JobExecution,_ и он получит статус _FAILED_:

![[Pasted image 20260418130750.png]]

Обработается три элемента, поскольку .chunk(3):

```
Executing step: [step1]
ru.sysout.batch.BatchConfig              : Processing dog
ru.sysout.batch.BatchConfig              : Processing cat
ru.sysout.batch.BatchConfig              : Processing fox
o.s.batch.core.step.AbstractStep         : Encountered an error executing step step1 in job job

org.springframework.batch.item.file.FlatFileParseException: Parsing error at line: 5 in resource=[file [C:\Code\sysout\batch0\src\main\resources\input.csv]], input=[,,,5,eagle]
```

В выходном файле будет три элемента.

В таблице _batch_step_execution_ видно, что считано 4 элемента, записано 3 и выполнен 1 commit:

![[Pasted image 20260418130906.png]]


Поскольку _JobExecution_ для последнего _JobInstance_ имеет статус _FAILED_, можно перезапустить задание с теми же параметрами (по сути выполнить тот же _JobInstance_ снова). Будет создан новый _JobExecution._ Причем **~={orange}задание начнет выполняться не сначала, а с проблемной точки.=~**

##### Рестарт

(починим файл):

```csv
1,dog
2,cat
3,fox
4,elephant
5,eagle
6,squirrel
7,tiger
8,shark
```

- В консоли видно, что обрабатываются 4-8 элементы. Первые три заново не обрабатываются:

```
Processing elephant
Processing eagle
Processing squirrel
Processing tiger
Processing shark
```

- Новый _batch_job_instance_ не появляется в базе.
- А появляется новый _batch_job_execution_ в статусе COMPLETED (для старого _batch_job_instance_).
- В таблице _batch_step_execution_ добавляется строка, где видно, что считаны и записаны оставшиеся 5 элементов, выполнено 2 commit:

![[Pasted image 20260418131144.png]]

----
#### [[JobInstance & scheduling - Spring Batch - Flashcards|Link to flashcards]]



---
### References:

