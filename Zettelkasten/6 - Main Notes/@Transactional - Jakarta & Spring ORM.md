
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

----
#### [[@Transactional - Jakarta & Spring - Flashcards|Link to flashcards]]



---
### References:

- [[Поддержка декларативных транзакций - Spring-orm]]