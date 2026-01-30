
Theory for the cards: [[Hibernate & JPA]]

FILE TAGS: jpa

Q: Как установить уровень изоляции транзакции в JPA?
A: В Hibernate уровень изоляции задаётся глобально через свойство конфигурации `hibernate.connection.isolation` (значения: 1=READ_UNCOMMITTED, 2=READ_COMMITTED, 4=REPEATABLE_READ, 8=SERIALIZABLE)
	
Либо устанавливаем уровень изоляции программно на самом соединении `java.sql.Connection`:
```java
EntityManager em = entityManagerFactory.createEntityManager();
	
Connection conn = em.unwrap(java.sql.Connection.class); conn.setTransactionIsolation(Connection.TRANSACTION_REPEATABLE_READ);
```
<!--ID: 1769429821808-->

Q: Что включает в себя аннотация `@Table`?
A: Аннотация `@Table` в JPA **определяет свойства таблицы базы данных**, с которой связана `@Entity`. Используется **опционально** — по умолчанию имя таблицы = имя класса.
	
```java
@Entity
@Table(
    name = "users",           // Имя таблицы (обязательный при отличии)
    schema = "public",        // Схема БД
    catalog = "mydb",         // Каталог/БД
    uniqueConstraints = {     // Уникальные ограничения
        @UniqueConstraint(
            name = "uk_email",
            columnNames = {"email"}
        )
    }
)
public class User { }
```
	
Здесь указание `@UniqueConstraint` нужно исключительно для ddl-auto - генерации БД из кода. 
	
> **NB!** Если работаем с готовой таблицей - это не добавляет дополнительных валидаций и использование `@UniqueConstraint` бессмысленно.
	
Также добавление unique costraint при ddl-auto можно сделать через аргумент аннотайии `@Column`:
	
```java
@Entity
@Table(uniqueConstraints = {...})  // Только для DDL (для составных Unique constrainst)
public class User {
    @Id 
    private Long id;
    
    @Column(unique = true)  // Только для одиночных полей
    private String email;
}
```
<!--ID: 1769763230329-->
