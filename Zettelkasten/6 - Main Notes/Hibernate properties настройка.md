
07-01-2026 10:29

Status:

Tags: [[Hibernate & JPA]] [[Java+]]

---
# Hibernate properties настройка


### Подключение к базе данных

```
hibernate.connection.driver_class = org.postgresql.Driver
hibernate.connection.url          = jdbc:postgresql://localhost:5432/mydb
hibernate.connection.username     = myuser
hibernate.connection.password     = secret
```


### Диалект базы данных

Обязательно указываем корректный Dialect для конкретной СУБД:

hibernate.dialect = org.hibernate.dialect.PostgreSQLDialect

 Примеры:
```
org.hibernate.dialect.H2Dialect
org.hibernate.dialect.MySQL8Dialect
org.hibernate.dialect.OracleDialect
org.hibernate.dialect.SQLServerDialect
```


---
## Управление схемой (DDL)

hbm2ddl.auto определяет, что делать со схемой при старте:
- none      : ничего не делать
- validate  : только проверить схему, без изменений
- update    : попытаться обновить схему под сущности
- create    : пересоздать схему при старте
-  create-drop : create + удалить схему при остановке

```properties
hibernate.hbm2ddl.auto = validate
``` 


---
## Логирование SQL

- Печатать SQL в лог:
```properties
hibernate.show_sql = true
```

- Форматировать SQL для читаемости:
```properties
hibernate.format_sql = true
```

- Показывать комментарии (если используются):
```properties
hibernate.use_sql_comments = false
```

Дополнительно через логгер (пример для Logback/Log4j):

```properties
org.hibernate.SQL=DEBUG        # сами SQL запросы
org.hibernate.type.descriptor.sql=TRACE  # значения параметров
```


---
## Кэширование

1. Первый уровень кэша (Session) всегда включён и не настраивается свойствами.
2. Второй уровень кэша:

```properties
hibernate.cache.use_second_level_cache = false
```

#### Кэш запросов (HQL/Criteria):
```properties
hibernate.cache.use_query_cache = false
```

Если включаем, нужен провайдер:

```properties
hibernate.cache.region.factory_class = org.hibernate.cache.ehcache.EhCacheRegionFactory
```

или, для современных конфигураций:

```properties
hibernate.cache.region.factory_class = org.hibernate.cache.jcache.JCacheRegionFactory
```


---
### Контекст текущей сессии

Как Hibernate будет привязывать Session к «текущему контексту»:

 - thread - `org.hibernate.context.internal.ThreadLocalSessionContext`: одна Session на поток. ~={orange}При работе со Spring ломает интеграцию Spring с транзакциями, заставляя Hibernate самому управлять сессией по ThreadLocal.=~

 - jta - `org.hibernate.context.internal.JTASessionContext`: интеграция с JTA. Одна Session на текущую JTA‑транзакцию.

- managed - `org.hibernate.context.internal.ManagedSessionContext`: - Hibernate сам по себе не открывает/закрывает Session, а ожидает, что это сделает внешний код, регистрируя её как «текущую». ~={orange}Для работы вручную.=~

```properties
hibernate.current_session_context_class = thread
```

> [!warning] При работе со Spring.
> **Правильно:** вообще **не указывать** это свойство в конфигурации Hibernate.
>
>Spring при создании `SessionFactory` подставит свою `CurrentSessionContext` 
>(класс `org.springframework.orm.hibernate5.SpringSessionContext` или аналогичная версия), которая:
> 
> - привязывает `Session` к текущему потоку в рамках `@Transactional`;
>    
> - позволяет безопасно использовать `sessionFactory.getCurrentSession()` внутри транзакционных методов.

~={green}Либо самому настроить TransactionManager (обычно `HibernateTransactionManager` или JPA‑вариант) и использовать=~ ~={yellow}@Transactional=~ -> [[Поддержка декларативных транзакций - Spring-orm]]


> [!tip]
> Вместо того чтобы вручную делать:
>```java
>Session session = sessionFactory.openSession(); // ... session.close();
>```
>
>можно везде писать:
>
>```java
>Session session = sessionFactory.getCurrentSession();
>```
>
>и Hibernate сам решает:
>
>- когда создавать эту Session;
>    
>- когда её закрыть;
>    
>- какую Session считать «текущей» в данном месте кода.

---
### Ленивая загрузка и fetching

Включить/отключить «ленивую» загрузку (обычно true):
```properties
hibernate.enable_lazy_load_no_trans = false
```

Размер batch-fetch для ленивых ассоциаций (например, по 16 элементов):
```properties
hibernate.default_batch_fetch_size = 16
```

###  JDBC fetch size (передаётся в Statement.setFetchSize):

0 = использовать драйвер/БД по умолчанию
```properties
hibernate.jdbc.fetch_size = 0
```


---
# JDBC batch-операции (insert/update)

Размер батча для JDBC (0 = отключено):
```properties
hibernate.jdbc.batch_size = 30
```

Разрешить batch для версионируемых сущностей:
```properties
hibernate.jdbc.batch_versioned_data = true
```

Упорядочивать insert’ы и update’ы по ключам для лучшего batching:
```properties
hibernate.order_inserts  = true
hibernate.order_updates  = true
```




----
#### [[Hibernate properties настройка - Flashcards|Link to flashcards]]



---
### References:

