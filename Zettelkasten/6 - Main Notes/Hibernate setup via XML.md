
04-11-2025 15:07

Status:

Tags:

---
# Hibernate setup via XML

У Hibernate есть два xml:

- `hibernate.cfg.xml` - hibernate-native

- `persistence.xml` - JPA standard

---

|Характеристика|**hibernate.cfg.xml**|**persistence.xml**|
|---|---|---|
|**Стандарт**|Проприетарный для Hibernate|Стандарт JPA (javax.persistence)|
|**Когда используется**|Если вы работаете напрямую с `SessionFactory`, `Configuration`, `Session`|Если вы используете JPA (`EntityManager`, `EntityManagerFactory`)|
|**Расположение**|Обычно в `src/main/resources` или в корне classpath|Обязательно в `META-INF/persistence.xml`|
|**Содержимое**|JDBC‑параметры (URL, логин, пароль), диалект, маппинги (`<mapping resource="..."/>`)|Определение persistence‑unit, список классов‑сущностей, JPA‑свойства (Hibernate‑специфичные можно добавлять через `<properties>`)|
|**Пример API**|`java Configuration cfg = new Configuration().configure(); SessionFactory sf = cfg.buildSessionFactory();`|`java EntityManagerFactory emf = Persistence.createEntityManagerFactory("myUnit"); EntityManager em = emf.createEntityManager();`|
|**Гибкость**|Глубокий доступ к специфике Hibernate|Унифицированный подход, переносимость между провайдерами (EclipseLink, OpenJPA и др.)|


### Пример Hibernate-native конфигурации с XML:

hibernate.cfg.xml:
```xml
<?xml version="1.0" encoding="utf-8"?>  
<!DOCTYPE hibernate-configuration SYSTEM  
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">  
  
<hibernate-configuration>  
    <session-factory>        <property name="hibernate.dialect">  
            org.hibernate.dialect.MySQLDialect  
        </property>  
        <property name="hibernate.connection.driver_class">  
            com.mysql.cj.jdbc.Driver  
        </property>  
  
        <property name="hibernate.connection.url">  
            jdbc:mysql://localhost:3306/hibernate  
        </property>  
        <property name="hibernate.connection.username">  
            YOUR_USERNAME_HERE (root by default)  
        </property>  
        <property name="hibernate.connection.password">  
            root  
        </property>  
        <property name="current_session_context_class">thread</property>  
        <property name="show_sql">true</property>  
  
        <mapping resource="Developer.hbm.xml"/>  
  
    </session-factory></hibernate-configuration>
```

И для каждого маппинга свой дополнительный XML-файл:

Developer.hbm.xml:
```xml
<?xml version="1.0" encoding="utf-8"?>  
<!DOCTYPE hibernate-mapping PUBLIC  
        "-//Hibernate/Hibernate Mapping DTD//EN"  
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">  
<hibernate-mapping>  
    <class name="ctp.web.hibernatexml.model.Developer" table="developers">  
        <id name="id" type="int" column="ID">  
            <generator class="native"/>  
        </id>        <property name="name" column="name" type="string"/>  
        <property name="specialty" column="specialty" type="string"/>  
        <property name="experience" column="experience" type="int"/>  
    </class></hibernate-mapping>
```


----
#### [[Hibernate setup via XML - Flashcards|Link to flashcards]]



---
### References:

