
15-09-2026 15:14

Status:

Tags: [[Kafka]] [[Java+]]

---
# Конфигурация Topic - Kafka

> [!note]
> Здесь в примерах указаны default значения, если они есть


> Где задаются: **при создании/изменении topic**, например через `kafka-topics.sh`, `kafka-configs.sh`, AdminClient, Terraform/Kafka Operator. Topic может использовать [[Конфигурация Broker - Kafka|broker default settings]] или иметь собственный override. 


```text
partitions
replication.factor
retention.ms
cleanup.policy
min.insync.replicas
retention.bytes
compression.type
max.message.bytes
```

- `partitions` — количество партиций topic; **обязательное значение при обычном создании**, если не используется default.
    
- `replication.factor=1` — количество реплик каждой партиции; если не указать при создании, используется `default.replication.factor`([[Конфигурация Broker - Kafka|broker default settings]]). 
    
- `retention.ms=604800000` — хранить сообщения максимум 7 дней.
	
- `retention.bytes=-1` — ограничения размера retention нет.
    
- `cleanup.policy=delete` — удалять старые сообщения согласно retention; альтернативно `compact`. 
    
- `min.insync.replicas=1` — минимум ISR для успешной записи при `acks=all`. 
    
- `compression.type=producer` — topic сохраняет compression codec, выбранный producer. 
    
- `max.message.bytes=1048588` — максимальный размер record batch для topic. 


> [!warning]
>  `segment.ms/segment.bytes` определяют, **когда Kafka создаёт новый segment**, а `retention.ms` — **когда старые segments могут быть удалены**.

- `segment.ms=604800000` — максимальное время жизни **одного активного log segment** до его закрытия и создания нового; по умолчанию 7 дней.

- `segment.bytes=1073741824` — максимальный размер одного **log segment**, по умолчанию 1 GiB.

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


Например:

```bash
kafka-topics.sh --create \
  --topic payments \
  --partitions 6 \
  --replication-factor 3 \
  --config retention.ms=259200000 \
  --config cleanup.policy=delete
```


---
### `cleanup.policy` & `retention.ms`


**Retention** — это временной лимит хранения сообщений в партиции (по времени или размеру). Сообщения удаляются, когда истекает `retention.ms` (например, 7 дней) или `retention.bytes` (например, 10 ГБ). Подходит для event streaming, где важны все события за период (клики, логи, транзакции).  
	  
**Log compaction** — это режим, при котором Kafka хранит **только последнее сообщение для каждого ключа**, удаляя старые дубли. Сообщения не удаляются по времени, а «сжимаются» до уникальных ключей. Требует `cleanup.policy=compact` и наличия ключей у сообщений. Подходит для storage use-case: кэш состояний (профиль пользователя, баланс счёта, конфигурация), где важно только текущее значение per key.  
	  
**Комбинированный режим** (`cleanup.policy=compact,delete`) — сообщения сохраняются и по retention, и compact: старые дубли удаляются, но сообщения старше retention тоже. Используется, когда нужно хранить последние значения, но с ограничением по времени (например, состояние с TTL).  
	  
segment  - активный файл, куда пишутся сообщения:
	
```
orders-0/
├── 00000000000000000000.log
├── 00000000000000000000.index
├── 00000000000000000000.timeindex
├── 00000000000001048576.log
├── 00000000000001048576.index
├── 00000000000001048576.timeindex
└── ...
```
	
Каждый `.log` — это один сегмент. Имя файла — это base offset первого сообщения в сегменте.
	
Например:
	
- `00000000000000000000.log` — сообщения с offset 0+
    
- `00000000000001048576.log` — сообщения с offset 1,048,576+
	
- `segment.ms` - Максимальное время жизни активного сегмента до его принудительного закрытия.
- `segment.bytes` - Максимальный размер сегмента.
	  
---  
**По умолчанию**  
- cleanup.policy=delete  
- retention.ms=604800000 (7 дней)  
- retention.bytes=-1 (не ограничено по размеру)  
- segment.ms=604800000 (7 дней)  
- segment.bytes= ... 1GiB  


----
#### [[Конфигурация Topic - Kafka - Flashcards|Link to flashcards]]



---
### References:

