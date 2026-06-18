
08-11-2025 11:50

Status: #child

Tags: [[Spring]]

---
# @Transactional - Jakarta & Spring ORM

Может ставиться над методами и классами.

Работает только в Spring контексте, т.к. в нём существуют BeanPostProcessors, которые и внедряют логику транзакций в методы. О том как они работают [[Про использование BeanPostProcessor - Spring|тут]].

Для корректной работы в Spring-контексте также нужно настроить JpaTransactionManager. Подробнее про настройку [[Поддержка декларативных транзакций - Spring-orm#Настройка менеджера транзакций Hibernate (JPA)|тут]].

---
### Поля аннотации

Spring передаёт установленные значения полей в используемый `JpaTransactionManager`.

`readOnly = {true, false}` 
	- `false` - по умолчанию
	- `true` - ставим, если транзакция не будет изменять данные -> **БД не создаёт снимки (snapshots) сущностей** для отслеживания изменений.

---

| Поле                            | Тип                  | Описание                                                                                                  |
| ------------------------------- | -------------------- | --------------------------------------------------------------------------------------------------------- |
| `value` / `transactionManager`  | `String`             | Имя TransactionManager (по умолчанию "transactionManager").​                                              |
| `propagation`                   | `Propagation`        | Распространение: `REQUIRED` (поумолчанию), `REQUIRES_NEW`, `NESTED`, `SUPPORTS` и др.​                    |
| `isolation`                     | `Isolation`          | Уровень изоляции: `DEFAULT`, `READ_COMMITTED`, `SERIALIZABLE` (действует для `REQUIRED`/`REQUIRES_NEW`).​ |
| `timeout` / `timeoutString`     | `int` / `String`     | Таймаут в секундах (0=безлимит).​                                                                         |
| `readOnly`                      | `boolean`            | Только чтение (`false` по умолчанию). `readOnly=true` - оптимизация для SELECT.​                          |
| `rollbackFor` / `noRollbackFor` | `Class<Throwable>[]` | Исключения для отката/неотката (по умолчанию `RuntimeException`, `Error`).​                               |


### `propagation` в `@Transactional` 

определяет, как метод участвует в транзакции:

- `REQUIRED` — использовать текущую транзакцию или создать новую.
    
- `REQUIRES_NEW` — всегда создавать новую, текущую приостанавливается.
    
- `MANDATORY` — метод должен вызываться внутри существующей транзакции.
    
- `SUPPORTS` — использовать текущую транзакцию, если она есть.
    
- `NOT_SUPPORTED` — выполнять без транзакции.
    
- `NEVER` — запрет на выполнение внутри транзакции.
    
- `NESTED` — вложенная транзакция через savepoint внутри текущей.

---
### Приоритет аннотаций

Аннотация на методе > аннотация на классе.

```java
@Service  
@Transactional(readOnly = true)  
public class PeopleService {  
  
	@Transactional   // по умолчанию readOnly = false -> в JpaTransactionManager передастся readOnly = false
    public void update(Person person, int id){ 
	    peopleRepo.update(person, int id);  
    }  
  
}
```


И ещё пример:

```java
@Service  
@Transactional  
public class FilingServiceImpl implements FilingService {  
  
    private final FilingRepository repository;  
  
    @Autowired  
    public FilingServiceImpl(FilingRepository repository) {  
        this.repository = repository;  
    }  
  
    @Override  
    public void createFiling(UUID recordId, Date filingFiscalYear, Date filingEndDate, Filing.FilingType filingType) {  
        Filing filing = new Filing(recordId, filingFiscalYear, filingType);  
  
        repository.save(filing);  
    }  
  
    @Override  
    public void deleteFiling(UUID recordId) {  
        repository.deleteById(recordId);  
    }  
  
    @Override  
    @Transactional(readOnly = true)    //тут аннотация метода, она имеет приоритет над аннотацией класса -> выполнится транзакция readOnly
    public Filing getTheMostRecentFiling(UUID recordId) {  
        return repository.findTheMostRecentFilingByRecordId(recordId);  
    }  
}
```


---
## Как работает под капотом (в прокси)

```java
@Service
public class PaymentService {

    @Transactional
    public void transfer(Long fromId, Long toId, BigDecimal amount) {
        debit(fromId, amount);
        credit(toId, amount);
    }
}
```


```java
public class PaymentServiceProxy extends PaymentService {

    private final PaymentService target;
    private final PlatformTransactionManager txManager;

    public PaymentServiceProxy(
            PaymentService target,
            PlatformTransactionManager txManager) {

        this.target = target;
        this.txManager = txManager;
    }

    @Override
    public void transfer(
            Long fromId,
            Long toId,
            BigDecimal amount) {

        TransactionStatus status =
                txManager.getTransaction(
                        new DefaultTransactionDefinition());

        try {
            target.transfer(fromId, toId, amount);

            txManager.commit(status);

        } catch (Throwable ex) {

            txManager.rollback(status);

            throw ex;
        }
    }
}
```



---

### Установка уровня изоляции транзакций 

**Через аннотацию ~={purple}@Transactional=~** - локально
```java
@Transactional(isolation = Isolation.REPEATABLE_READ)
```
Spring автоматически применит настройку к Connection через JpaTransactionManager. 


**Глобально:** 
в `persistence.xml` или `application.properties` через `spring.jpa.properties.hibernate.connection.isolation=4`.


----
#### [[@Transactional - Jakarta & Spring - Flashcards|Link to flashcards]]



---
### References:

- [[Поддержка декларативных транзакций - Spring-orm]]
- [[ACID principles - БД]]
