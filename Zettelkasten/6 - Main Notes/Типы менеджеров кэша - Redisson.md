
19-03-2026 17:47

Status:

Tags: [[Redisson]] [[Spring Data JPA]] [[Hibernate & JPA]]

---
# Типы менеджеров кэша - Redisson



## Основные менеджеры кэша

Redisson предлагает несколько CacheManager'ов для Spring Data:

- **RedissonSpringCacheManager** — базовый open-source вариант без локального кэша.
    
- **RedissonSpringLocalCachedCacheManager** (PRO) — с near cache для ускорения чтения до 45x, синхронизацией через pub/sub.​
    
- Версии для кластера: **RedissonClusteredSpringLocalCachedCacheManager** (PRO) с partitioning данных.​
    

PRO-версии добавляют scripted eviction, advanced eviction и поддержку native eviction (Redis 7.4+/Valkey 9.0+).

----
#### [[Типы менеджеров кэша - Redisson - Flashcards|Link to flashcards]]



---
### References:

