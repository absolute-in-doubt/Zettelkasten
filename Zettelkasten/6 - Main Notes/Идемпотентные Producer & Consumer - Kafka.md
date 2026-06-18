
01-02-2026 17:01

Status:

Tags: [[Kafka]] [[Java+]]

---
# Идемпотентные Producer & Consumer - Kafka

### Идемпотентный Producer


**Настройки**: 

```
enable.idempotence=true
acks=all                    # Все ISR подтвердили
retries=Integer.MAX_VALUE   # Бесконечные повторы

# Интервал между повторными попытками
retry.backoff.ms=100        # Базовый интервал 100 мс

# Общий тайм-аут доставки сообщения
delivery.timeout.ms=120000  # Ожидание до 2 минут

max.in.flight.requests.per.connection<=5  # Ограничение очередей
```


**Producer получает**:

1. **PID** (Producer ID) — уникальный идентификатор Producer'а на брокере
    
2. **Sequence Number** — монотонно растущий номер для каждого сообщения в partition

```
Producer → Kafka:
Message1: PID=1001, Seq=1
Message2: PID=1001, Seq=2  
Message3: PID=1001, Seq=3
```

Как брокер определяет дубликаты

```
Брокер хранит: (PID + partition) → last_sequence_number

Producer retry Message2 (Seq=2):
1. Брокер видит: Seq=2 == last_sequence=1 + 1 ✅
2. Записывает Message2 (Seq=2)
3. Обновляет last_sequence=2

Producer retry Message1 (Seq=1) снова:
1. Брокер видит: Seq=1 < last_sequence=2 ❌
2. **Игнорирует дубликат**
```

> [!warning] **Idempotent Producer не делает Exactly-Once!**
> ```
> ❌ Без Outbox (At-Least-Once + Idempotent Producer):
>1. INSERT INTO orders (id=123, status='created') ✅
>2. producer.send("orders-created", event) ❌ Kafka упал
>
>Результат:
>- Заказ в БД ✅
>- Событие в Kafka ❌ ← PaymentService не узнает!
> ```
> 
> Это проблема Dual Writes.

---
### Идемпотентный Consumer

**В Kafka нет "идемпотентного Consumer" как настройки Producer'а.** Идемпотентность Consumer'а реализуется **приложением** ([[InBox, OutBox patterns - Patterns#|Inbox pattern]]).

----
#### [[Идемпотентные Producer & Consumer - Kafka - Flashcards|Link to flashcards]]



---
### References:

- [[InBox, OutBox patterns - Patterns]]