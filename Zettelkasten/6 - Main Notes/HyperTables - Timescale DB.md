
22-11-2025 09:55

Status:

Tags: [[Timescale DB]] [[Базы данных]]

---
# HyperTables - Timescale DB

Таблица таблиц.

HyperTable - абстрактная таблица, состоящая из меньшиз обычных (standard Postgres tables), которые называются chunks.

Каждый chunk имеет свою дату (диапазон дат) (или по другим измерениям (dimensions)). 

![[Pasted image 20251122100321.png]]

При запросе на получение данных:
1. определяет нужный chunk
2. вызывает запрос на нём.

~={red}!!!!=~ Не проходит по всей HyperTable ~={red}!!!!=~


---
### Преимущества HyperTables


- **Efficient data management with [automated partitioning by time](https://docs.tigerdata.com/use-timescale/latest/hypertables/improve-query-performance/#optimize-hypertable-chunk-intervals/)** - разделяет данные на chunks (по значениям даты в определённом диапазоне)


- **Better performance with [strategic indexing](https://docs.tigerdata.com/use-timescale/latest/hypertables/hypertables-and-unique-indexes/)** - [[Indexes - SQL|индекс]] по времени в порядке убывания ~={cyan}создаётся автоматически=~ при создании HyperTable. Также создаются вспомогательные индексы на уровне chunk-ов.


- **Faster queries with [chunk skipping](https://docs.tigerdata.com/use-timescale/latest/hypertables/improve-query-performance/)** - Timescale пропускает chunks, которые не подходят по времени (или другому  используемомому dimension) ->  ~={red}!!!!=~ Не проходит по всей HyperTable ~={red}!!!!=~


- **Advanced data analysis with [hyperfunctions](https://docs.tigerdata.com/use-timescale/latest/hyperfunctions/)** -  Tiger Cloud enables you to efficiently process, aggregate, and analyze significant volumes of data while maintaining high performance.


---
### TIme partitioning 

> [!note]
>Для времени желательно использовать `timestamptz` - `timestamp` + `time zone` (тип данных Postgres).
>
>Но также можно и `date`, `integer`, `timestamp`

By default -  каждый chunk хранит данные за 7 дней (`chubk_interval = 7`)

Каждый chunk хранит данные только в определённом промежутке. 

Если вставляется новое значение, которое по времени не подходит ни одному из существующих chunk-ов -> создаётся новый chunk.

----
#### [[HyperTables - Timescale DB - Flashcards|Link to flashcards]]



---
### References:

