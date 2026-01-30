
Index: [[Java+]]

В Java `DataSource` — это интерфейс из `javax.sql`, описывающий фабрику подключений к БД, а конкретные реализации различаются набором свойств и тем, используют ли они пул соединений или каждый раз открывают новое.

Сам интерфейс `javax.sql.DataSource` определяет только метод `getConnection(...)`


Поля DataSource:
- **name**: JNDI‑имя ресурса `java:global/...`, `java:app/...` и т.п.​
    
- **className**: имя класса, реализующего `javax.sql.DataSource` / `XADataSource` / `ConnectionPoolDataSource`.​
    
- **url** / **databaseName** / **serverName** / **portNumber**: параметры адреса БД.​
    
- **user** / **password**: учетные данные для подключения.​
    
- **maxStatements** и другие параметры пула (зависят от конкретного провайдера).


### Типы реализаций DataSource

- Обычный `DataSource`: выдаёт `Connection`, но сам может не иметь пула.​
	- [[DriverManagerDataSource]] - реализован Spring

- `ConnectionPoolDataSource`: низкоуровневый источник для реализации пулов; оборачивается в `DataSource` пулом (Tomcat JDBC, HikariCP и др.).
	- [[HikariDataSource]]

- `XADataSource`: источник XA‑подключений для распределённых транзакций.