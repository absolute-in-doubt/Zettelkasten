
08-11-2025 10:35

Status: #child

Tags: [[Spring]]

---
# setup - Spring Data


### В конфигурационном классе

Добавляем аннотацию ~={purple}@EnableJpaRepositories(`path to repository package`)=~ 

~={purple}@EnableJpaRepositories(`path to repository package`)=~  - указывает путь для сканирования аннотаций ~={purple}@Repository=~

> [!note] 
> Для работы с [[Поддержка декларативных транзакций - Spring-orm|декларативными транзакциями]] (TransactionManager) при работе с Spring Data работаем **исключительно с интерфейсами JPA**
> 
> И еще. ~={red}**Spring Data ищет бин с именем исключительно** `entityManagerFactory`.=~ т.е. метод должен называться именно так:
> ```java
> @Bean  
>public LocalContainerEntityManagerFactoryBean entityManagerFactory(){  
>    LocalContainerEntityManagerFactoryBean emf = new LocalContainerEntityManagerFactoryBean();  
>  
>    emf.setDataSource(dataSource());  
>    emf.setPackagesToScan("org.bsuir.spring.data.crud");  
>    emf.setJpaVendorAdapter(new HibernateJpaVendorAdapter());  
>    emf.setJpaProperties(hibernateProperties());  
>  
>    System.out.println("initialized localEMF");  
>    return emf;  
>}
> ```
>
>А также ищет **~={red}JpaTransactionManager с именем бина исключительно `transactionManager`:=~**
>```java
>@Bean  
>public JpaTransactionManager transactionManager(){  
 >   System.out.println("Started initting JTM");  
>    //JpaTransactionManager transactionManager = new JpaTransactionManager(emf);  
>    JpaTransactionManager transactionManager = new JpaTransactionManager(Objects.requireNonNull(entityManagerFactory().getObject()));  
>    System.out.println("initialized JTM");  
>    return transactionManager;  
>}
>```


Создаём интерфейс, расширяющий `JpaRepository<EntityType, PrimaryKeyType>`:

~={purple}@Repository=~ - обозначает, что это JpaRepository

```java
@Repository
public interface PeopleJpaRepository extends JpaRepository<Person, Integer> {  
}
```

----
#### [[setup - Spring Data - Flashcards|Link to flashcards]]



---
### References:

- [[@Repository - Spring Data]]
