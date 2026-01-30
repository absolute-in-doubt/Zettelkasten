
06-01-2026 11:47

Status:

Tags: [[DataSource]] [[Java+]]

---
# HikariDataSource

HikariCP — это библиотека пула JDBC‑соединений, и в ней есть своя реализация `DataSource`: `com.zaxxer.hikari.HikariDataSource`.

---

- `HikariDataSource` реализует `javax.sql.DataSource` и использует внутренний высокопроизводительный пул.

- Конфигурация идёт через `HikariConfig` (или свойства `spring.datasource.hikari.*` в Spring Boot: `maximum-pool-size`, `minimum-idle`, `connection-timeout`, `data-source-properties.*` и др.).​
    

#### Отличия от `DriverManagerDataSource`:​

- `DriverManagerDataSource` каждый раз открывает новое соединение → подходит только для простых случаев, медленно под нагрузкой.
    
- `HikariDataSource` держит пул готовых подключений, минимизирует задержки и хорошо оптимизирован под высокую нагрузку.
    

В Spring Boot HikariCP сейчас является «дефолтным» пулом: если он есть в classpath и нет других пулов, Boot сам создаст `HikariDataSource` как основной `DataSource`


### Примеер конфигурации HikariDataSource

```java
import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;

import javax.sql.DataSource;

public class DataSourceConfig {

    public DataSource hikariDataSource() {
        HikariConfig config = new HikariConfig();

        // Базовые JDBC-параметры
        config.setJdbcUrl("jdbc:postgresql://localhost:5432/mydb");
        config.setUsername("dbuser");
        config.setPassword("dbpass");
        config.setDriverClassName("org.postgresql.Driver");

        // Основные настройки пула
        config.setMaximumPoolSize(10);          // максимум соединений в пуле
        config.setMinimumIdle(2);               // минимум свободных соединений
        config.setConnectionTimeout(30000);     // таймаут ожидания соединения (мс)
        config.setIdleTimeout(600000);          // таймаут простоя соединения (мс)
        config.setMaxLifetime(1800000);         // максимум «жизни» соединения (мс)

        // Дополнительные свойства конкретного драйвера (пример)
        config.addDataSourceProperty("cachePrepStmts", "true");
        config.addDataSourceProperty("prepStmtCacheSize", "250");
        config.addDataSourceProperty("prepStmtCacheSqlLimit", "2048");

        return new HikariDataSource(config);
    }
}

```

---
### References:

