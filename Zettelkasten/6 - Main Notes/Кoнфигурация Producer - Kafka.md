
15-09-2026 15:27

Status:

Tags: [[Kafka]] [[Java+]]

---
# Кoнфигурация Producer - Kafka



> [!note]
> Здесь в примерах указаны default значения, если они есть


> Задаются **на стороне Java-приложения**, обычно в `application.yml`:
>
>```yaml
>spring:
>  kafka:
>    producer:
>      ...
>```
>или непосредственно в `ProducerConfig`.


```text
bootstrap.servers
key.serializer
value.serializer
acks
enable.idempotence
retries
batch.size
linger.ms
compression.type
buffer.memory
max.in.flight.requests.per.connection
delivery.timeout.ms
request.timeout.ms
max.request.size
```

- `bootstrap.servers` — адреса Kafka brokers для первоначального подключения; default — пустое значение.
    
- `key.serializer` — serializer ключа; **default отсутствует**.
    
- `value.serializer` — serializer value; **default отсутствует**.
    
- `acks=all` — producer ждёт подтверждения от ISR; текущий default. 
    
- `enable.idempotence=true` — идемпотентная запись, предотвращает дубликаты при retry; default `true`, если нет конфликтующих настроек. 
    
- `retries` — количество повторных попыток отправки; default — очень большое значение/фактически ограничивается `delivery.timeout.ms` в современных клиентах.
    
- `batch.size=16384` — размер batch для одной partition, 16 KiB.
    
- `linger.ms=5` — сколько producer может подождать для накопления batch; default 5 ms в Kafka 4.x. 
    
- `compression.type=none` — compression на стороне producer; **это клиентская настройка**, поэтому здесь она не дублируется с Topic.
    
- `buffer.memory=33554432` — память producer под буферизацию, 32 MiB.
    
- `max.in.flight.requests.per.connection=5` — максимум неподтверждённых requests на connection. 
    
- `delivery.timeout.ms=120000` — максимум времени от `send()` до успешного/неуспешного результата, 2 минуты.
    
- `request.timeout.ms=30000` — timeout ожидания ответа broker.
    
- `max.request.size=1048576` — максимальный размер request, 1 MiB.
    

> [!warning]
>  `compression.type` существует и как Topic config, и как Producer config. Здесь я отношу его к **Producer**, потому что в контексте клиента это выбор compression при отправке. Topic `compression.type=producer` лишь говорит broker сохранять выбранный producer codec. 



----
#### [[Кoнфигурация Producer - Kafka - Flashcards|Link to flashcards]]



---
### References:

