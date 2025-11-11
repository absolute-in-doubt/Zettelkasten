
02-11-2025 13:00

Status: #adult 

Tags: [[Spring]] [[Hibernate & JPA]]

---
# Настройка Hibernate со spring-orm

Для настройки используем настройку отдельно DataSource и отдельно Properties с настройками самого Hibernate:

```java
@Bean  
public DataSource dataSourceSimplified() {  
    DriverManagerDataSource ds = new DriverManagerDataSource();  
    ds.setDriverClassName("org.postgresql.Driver");  
    ds.setUrl("jdbc:postgresql://127.0.0.1:5432/people_database_2");  
    ds.setUsername("postgres");  
    ds.setPassword("root");  
    return ds;  
}
  
@Bean  
public Properties hibernateProperties() {  
    Properties properties = new Properties();  
    properties.put("hibernate.dialect", env.getRequiredProperty("hibernate.dialect"));  
    properties.put("hibernate.show_sql", env.getRequiredProperty("hibernate.show_sql"));  
    return properties;  
}
```

> [!warning] 
> При настройке Hibernate и PlatformTransactionManager,~={red} **в `hibernate.properties` указываем только настройки hibernate**=~ (типа диалекта и прочего). ~={red}**НИКАКИХ НАСТРОЕК СОЕДИНЕНИЯ**=~. Это исключительно в DataSource + берём из другого конфигурационного файла. 
> 
> Если передать одновременно и DataSource с указанными параметрами соединения и они будут в `hibernate.properties` -> Hibernate, _долбоёб блять_, возьмёт _какого-то хуя_ системное имя пользователя. _Гениально нахуй_. 
> 
> Даже если явно не давать ему читаить этот `hibernate.properties` -> он считает его автоматически.


### Создание EntityManagerFactory bean

(создаётся самим spring-orm):

```java
    @Bean
    public LocalEntityManagerFactoryBean entityManagerFactory() {
        LocalEntityManagerFactoryBean factoryBean = new LocalEntityManagerFactoryBean();
        // имя persistence-unit из persistence.xml
        factoryBean.setPersistenceUnitName("myPersistenceUnit");
        return factoryBean;
    }
    
    //или так:
    
       @Bean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory(DataSource dataSource) {
        LocalContainerEntityManagerFactoryBean emf = new LocalContainerEntityManagerFactoryBean();
        emf.setDataSource(dataSource());
        emf.setPackagesToScan("com.example.model"); // ищет @Entity классы
        emf.setJpaVendorAdapter(new HibernateJpaVendorAdapter());

        emf.setJpaProperties(hibernateProperties());

        return emf;
    }
```

### Создание SessionFactory bean

(создаётся самим spring-orm):

```java
    @Bean
    public LocalSessionFactoryBean sessionFactory(DataSource dataSource) {
        LocalSessionFactoryBean factoryBean = new LocalSessionFactoryBean();
        factoryBean.setDataSource(dataSource);
        factoryBean.setPackagesToScan("com.example.model"); // пакет с @Entity
        factoryBean.setHibernateProperties(hibernateProperties());
        return factoryBean;
    }
```

----
#### [[Настройка Hibernate со spring-orm - Flashcards|Link to flashcards]]



---
### References:

- [[Поддержка декларативных транзакций - Spring-orm]]