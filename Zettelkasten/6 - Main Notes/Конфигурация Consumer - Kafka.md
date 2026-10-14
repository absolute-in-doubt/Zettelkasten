
15-09-2026 15:45

Status:

Tags: [[Kafka]] [[Java+]]

---
# Конфигурация Consumer - Kafka


> [!note]
> Здесь в примерах указаны default значения, если они есть



>Задаются **на стороне Java-приложения**, обычно:
>
>```yaml
>spring:
>  kafka:
>    consumer:
>      ...
>```
>
>или непосредственно в `ConsumerConfig`.

```text
bootstrap.servers
key.deserializer
value.deserializer
group.id
auto.offset.reset
enable.auto.commit
auto.commit.interval.ms
max.poll.records
max.poll.interval.ms
session.timeout.ms
heartbeat.interval.ms
fetch.min.bytes
fetch.max.bytes
max.partition.fetch.bytes
isolation.level
```

- `bootstrap.servers` — brokers для первоначального подключения; default — пустое значение.
    
- `key.deserializer` — deserializer ключа; **default отсутствует**.
    
- `value.deserializer` — deserializer value; **default отсутствует**.
    
- `group.id` — идентификатор Consumer Group; default `null`.
    
- `auto.offset.reset=latest` — куда начинать чтение, если offset отсутствует; default `latest`. 
    
- `enable.auto.commit=true` — автоматически коммитить offsets; default `true`. 
    
- `auto.commit.interval.ms=5000` — период auto commit, 5 секунд. 
    
- `max.poll.records=500` — максимум records за один `poll()`.
    
- `max.poll.interval.ms=300000` — максимальный интервал между `poll()`, 5 минут.
    
- `session.timeout.ms=45000` — timeout определения отказа consumer при classic group protocol.
    
- `heartbeat.interval.ms=3000` — интервал heartbeat; используется с classic protocol.
    
- `fetch.min.bytes=1` — минимум данных, которые broker должен вернуть в fetch response. 
    
- `fetch.max.bytes=52428800` — максимум данных за один fetch, 50 MiB.
    
- `max.partition.fetch.bytes=1048576` — максимум данных с одной partition за fetch, 1 MiB.
    
- `isolation.level=read_uncommitted` — читать и committed, и aborted transactional messages; `read_committed` — только committed.
    


----
#### [[Конфигурация Consumer - Kafka - Flashcards|Link to flashcards]]



---
### References:

