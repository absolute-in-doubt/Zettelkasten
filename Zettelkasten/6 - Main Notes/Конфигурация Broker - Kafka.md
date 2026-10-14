
15-09-2026 15:05

Status:

Tags: [[Kafka]] [[Java+]]

---
# Конфигурация Broker - Kafka


> [!note]
> Здесь в примерах указаны default значения, если они есть


> Задаются **на Kafka Broker**, обычно `server.properties`; 
> в Docker/Kubernetes — через environment variables / Helm values.

```text
node.id 
process.roles
broker.id
listeners
advertised.listeners

num.partitions
default.replication.factor
auto.create.topics.enable
min.insync.replicas
unclean.leader.election.enable

log.dirs 

log.retention.ms 
log.retention.bytes 
log.cleanup.policy 

log.segment.bytes 
log.roll.ms
```

- `node.id=1` — уникальный ID broker'а. В KRaft используется вместо старого `broker.id`; default `-1` для `broker.id`, `node.id` в современных KRaft-конфигурациях задаётся явно.
	
- `process.roles=` — роли Kafka-процесса: `broker`, `controller` или обе; для KRaft обязательная инфраструктурная настройка.
	
- `broker.id` — идентификатор broker.
    
- `listeners` — адреса/протоколы, на которых broker принимает соединения.
    
- `advertised.listeners` — адреса, которые broker сообщает клиентам для подключения.


- `num.partitions=1` — количество партиций по умолчанию при автоматическом создании topic. 
    
- `default.replication.factor=1` — replication factor по умолчанию для автоматически создаваемых topic. 
    
- `auto.create.topics.enable=true` — разрешает автоматическое создание topic. 
    
- `min.insync.replicas=1` — минимальное количество ISR для успешной записи при `acks=all`.
	
- `unclean.leader.election.enable=false` — разрешать ли выбирать leader из реплик, не входящих в ISR; default `false`


- `log.dirs=null` — директории хранения Kafka logs; если не задано, используется `log.dir`, default `/tmp/kafka-logs`.


- `log.retention.ms=604800000` — хранить сообщения 7 дней по умолчанию.
	
- `log.retention.bytes=-1` — максимальный размер логов; `-1` означает **без ограничения по размеру**.
	
- `log.cleanup.policy=delete` — политика очистки логов по умолчанию.


- `log.segment.bytes=1073741824` — размер одного log segment, 1 GiB.
	
- `log.roll.ms` — максимальное время до rollover текущего segment; если не задан, используется `log.roll.hours=168` (**7 дней**).


### Overrides on the topic level

|Broker default|Topic override|
|---|---|
|`log.cleanup.policy`|`cleanup.policy`|
|`compression.type`|`compression.type`|
|`compression.gzip.level`|`compression.gzip.level`|
|`compression.lz4.level`|`compression.lz4.level`|
|`compression.zstd.level`|`compression.zstd.level`|
|`log.cleaner.delete.retention.ms`|`delete.retention.ms`|
|`log.segment.delete.delay.ms`|`file.delete.delay.ms`|
|`log.flush.interval.messages`|`flush.messages`|
|`log.flush.interval.ms`|`flush.ms`|
|`log.index.interval.bytes`|`index.interval.bytes`|
|`log.cleaner.max.compaction.lag.ms`|`max.compaction.lag.ms`|
|`log.cleaner.min.cleanable.ratio`|`min.cleanable.dirty.ratio`|
|`log.cleaner.min.compaction.lag.ms`|`min.compaction.lag.ms`|
|`min.insync.replicas`|`min.insync.replicas`|
|`log.preallocate`|`preallocate`|
|`log.retention.bytes`|`retention.bytes`|
|`log.retention.ms`|`retention.ms`|
|`log.segment.bytes`|`segment.bytes`|
|`log.index.size.max.bytes`|`segment.index.bytes`|
|`log.roll.jitter.ms`|`segment.jitter.ms`|
|`log.roll.ms`|`segment.ms`|
|`message.max.bytes`|`max.message.bytes`|
|`log.message.timestamp.after.max.ms`|`message.timestamp.after.max.ms`|
|`log.message.timestamp.before.max.ms`|`message.timestamp.before.max.ms`|
|`log.message.timestamp.type`|`message.timestamp.type`|




----
#### [[Конфигурация Broker - Kafka - Flashcards|Link to flashcards]]



---
### References:

