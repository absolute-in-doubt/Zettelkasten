
07-05-2026 14:39

Status:

Tags: [[Jedis]]

---
# short overview - Redis

Redis — in-memory хранилище данных, ключевое преимущество — задержка всего около милисекунды. 

Архитектурно: однопоточная модель (event loop), данные в RAM, поддержка персистентности через RDB snapshots и AOF логи. 

Типы данных: String, List, Set, Sorted Set, Hash, Bitmap, HyperLogLog, Geo, Streams, TimeSeries — всего 11+ структур.

Масштабирование:
- Master-Replica (чтение с реплик)
- Redis Cluster (sharding по hash slots)  
- Sentinel (HA с автоматическим failover)

В проектах использовал:
1. Кэш API-ответов сторис (Redis GET/SETEX) — p99 с 450мс → 80мс
2. Кэш шаблонов уведомлений (HGETALL шаблоны, TTL 1ч)
3. Rate limiting для админ-API (INCR + EXPIRE)
4. Inbox для Kafka consumer (RPOPLPUSH)

Инвалидация: TTL, LRU/LFU eviction, ключевые пространства (user:123:*) + lazy deletion через cache-aside pattern.



----
#### [[short overview - Redis - Flashcards|Link to flashcards]]



---
### References:

