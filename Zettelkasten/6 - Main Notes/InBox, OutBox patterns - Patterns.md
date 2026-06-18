
01-02-2026 16:51

Status:

Tags: [[Patterns]] [[Kafka]] [[Java+]]

---
# InBox, OutBox patterns - Patterns

> [!example] **Почему не нереляционные БД + удаление по TTL?**
> **Реляционные БД используются для Inbox/Outbox из-за **ACID-транзакций** и **атомарности** "бизнес-данные + событие".** TTL и upsert хороши для простых очередей, но **не дают гарантий консистентности** в микросервисах.


#### Использует [[Гарантии доставки сообщений - Kafka#At Least Once|At-Least Once]] в Kafka

Почему именно At-Least-Once

```
Inbox/Outbox решают Dual Writes → 
Kafka может дублировать сообщения → 
Inbox фильтрует дубликаты по event_id

Exactly-Once избыточно + накладно
```

##### Пример настроек для At-Least Once

**Producer (Outbox → Kafka Relay)**:

```java
acks = "all"           // Все реплики подтвердили
retries = Integer.MAX  // Повторы при сбоях
enable.idempotence = false  // Не обязательно
```

**Consumer (Kafka → Inbox)**:

```java
enable.auto.commit = false
isolation.level = "read_committed"  // Только committed сообщения
```

### Полная схема гарантий

```
1. БД-транзакция (Outbox INSERT)           → ACID
2. Relay: Outbox → Kafka (At-Least-Once)   → Inbox обработает дубли
3. Kafka Consumer → Inbox INSERT           → ACID  
4. Business logic                           → ACID (локальная БД)
5. Inbox UPDATE processed=true             → ACID

Итоговая семантика: ✅ Exactly-Once
```

### Зачем не Exactly-Once в Kafka

```
❌ Exactly-Once Producer:
- Транзакции (beginTransaction/commitTransaction)
- ProducerStateManager (100MB+ памяти)
- Производительность -30-50%

✅ At-Least-Once + Inbox:
- Простая конфигурация
- Высокая производительность  
- Inbox решает дубликаты дешево (UNIQUE INDEX)
```

----
#### [[InBox, OutBox patterns - Patterns - Flashcards|Link to flashcards]]



---
### References:

- [[InBox, OutBox implementation - Patterns]]