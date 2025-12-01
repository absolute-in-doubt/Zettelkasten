
02-11-2025 12:44

Status: #child

Tags: [[Spring]]

---
# Поддержка декларативных транзакций - Spring-orm

### ~={orange}Обычно [[@Transactional на методах service - Spring|@Transactional указывается на методах service]] для эффективной работы с каскадированием=~

Для поддержки декларативных транзакций необходимо над классом аннотированным как ~={purple}@Configuration=~ поставить аннотацию ~={purple}@EnableTransactionManagement=~:

```java
@Configuration  
@ComponentScan("org.bsuir.spring.data.crud")  
@PropertySource("classpath:hibernate.properties")  
@EnableTransactionManagement  
@EnableWebMvc  
public class SpringConfig implements WebMvcConfigurer {
...
}
```


~={purple}@PersistenceContext=~ - ставится над полем EntityManager, которое автоматически инжектится в класс, где мы используем ~={purple}@Transactional=~.

> [!warning]
> Чтобы аннотации `@Transactional` работали, надо, чтобы EntityManager управлялся Spring-ом, а не вручную.
> 
> Неправильно:
> ```java
> @Transactional  
>public void delete(int id){  
>    EntityManager em = emf.createEntityManager();  
>    Person person = em.find(Person.class, id);  
>    em.remove(person);  
>}
> ```
> Правильно:
> ```java
>    @PersistenceContext
>    private EntityManager em;
>
>    @Transactional
>    public void delete(int id) {
>        Person person = em.find(Person.class, id);
>        em.remove(person);
>    }
> ```


---
### Настройка менеджера транзакций Hibernate:

```java
@Bean  
public LocalSessionFactoryBean localSessionFactoryBean(){  
    LocalSessionFactoryBean factoryBean = new LocalSessionFactoryBean();  
    factoryBean.setDataSource(dataSource());  
    factoryBean.setHibernateProperties(hibernateProperties());  
    factoryBean.setPackagesToScan("org.bsuir.spring.data.crud");  
    return factoryBean;  
}  
  
@Bean 
public PlatformTransactionManager hibernateTransactionManager() {  
    HibernateTransactionManager transactionManager = new HibernateTransactionManager();  
    transactionManager.setSessionFactory(localSessionFactoryBean().getObject());  
    return transactionManager;  
}
```

> [!note]
> Не используем `Hibernate.properties` для настройки, т.к. HIbernate читает его автоматически. А если он получает данные (даже одинаковые) из нескольких источников -> он мутит знатные приколы

### Настройка менеджера транзакций Hibernate (JPA):

```java
       @Bean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory(DataSource dataSource) {
        LocalContainerEntityManagerFactoryBean emf = new LocalContainerEntityManagerFactoryBean();
        emf.setDataSource(dataSource());
        emf.setPackagesToScan("com.example.model"); // ищет @Entity классы
        emf.setJpaVendorAdapter(new HibernateJpaVendorAdapter());

        emf.setJpaProperties(hibernateProperties());

        return emf;
    }

    @Bean
    public JpaTransactionManager transactionManager(EntityManagerFactory emf) 
    {
        return new JpaTransactionManager(emf);
    }
```


---
## Использование транзакций

До:

```java
public void delete(int id){  
    EntityManager em = emf.createEntityManager();  
    em.getTransaction().begin();
    Person person = em.find(Person.class, id);  
    em.remove(person);  
    em.getTransaction().commit();
    em.close();
}
```

~={pink}Как Spring понимает, что нужно создать  EntityManager? В Configuration классе мы никак не обозначили, что LocalContainerEntityManagerFactory - это именно FactryBean.=~

После:
```java
    @PersistenceContext
    private EntityManager em;

    @Transactional
    public void delete(int id) {
        Person person = em.find(Person.class, id);
        em.remove(person);
    }
```

Все строки кода в методе, аннотированном ~={purple}@Transactional=~ выполняются в рамках транзакции.

Для методов, которые только читают данные из БД, можно добавить параметр `readOnly=true` -  немного ускорит транзакцию в некоторых случаях (при [[ACID principles - БД#Уровни изоляции|изоляции транзакций]].


----
#### [[Поддержка декларативных транзакций - Spring-orm - Flashcards|Link to flashcards]]



---
### References:

- [[@Transactional - Jakarta & Spring ORM]]