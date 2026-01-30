
06-01-2026 11:45

Status:

Tags: [[DataSource]] [[Java+]]

---
# DriverManagerDataSource

`org.springframework.jdbc.datasource`

Одна из нескольких стандартных реализаций DataSource, предлагаемых Spring.


---

`DriverManagerDataSource` — простая реализация `DataSource`, которая:​

- Конфигурируется через свойства `driverClassName`, `url`, `username`, `password`.​

- Для каждого вызова `getConnection()` вызывает `DriverManager` и возвращает **новое** JDBC‑подключение, то есть **это не пул** соединений.​


Её обычно используют:​

- В тестах или простых демо.
    
- В однопоточных/утилитарных сценариях, где нет нагрузки и не нужен пул.

---
### References:

