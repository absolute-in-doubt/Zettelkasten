
15-02-2026 12:18

Status:

Tags: [[Hibernate & JPA]] [[Java+]]

---
# Кэширование - JPA

В JPA (через Hibernate как провайдер) есть встроенная система кэширования на уровне сущностей, но без аннотаций вроде `@Cacheable`. Это **кэш первого (L1) и второго (L2) уровня**, а также **query cache**. Они оптимизируют загрузку из БД.

## Кэш первого уровня (L1)

**Назначение**: Автоматический кэш на уровне `EntityManager` (Session в Hibernate). Хранит сущности в рамках одной транзакции/сессии.  

Данные в кэше хранятся в виде Key-Value пар: 
- Key (EntityClass, EntityId) 
- Value (экземпляр сущности)

~={red}**Включён всегда**=~. Поиск: сначала L1, потом БД.  

***Пример***: 
	Загрузка User по ID в одной сессии — повторные вызовы берут из кэша.


> [!warning]
> Bulk-UPDATE & bulk-DELETE выполняются напрямую в БД, минуя кеш.


---
## Кэш второго уровня (L2)

> [!example] Note
> Hibernate не поставляет реализацию L2 кэша из коробки — для этого нужно подключить любую из доступных реализаций. Самые популярные из них описаны [здесь](https://docs.jboss.org/hibernate/core/3.3/reference/en/html/performance.html#performance-cache). Мы же будем использовать ehcache:
>```
><dependency>
>	<groupId>org.hibernate</groupId>
>	<artifactId>hibernate-jcache</artifactId>
>	<version>7.0.0</version>
></dependency>
><dependency>
>	<groupId>org.ehcache</groupId>
>	<artifactId>ehcache</artifactId>
>	<version>3.10.1</version>
>	<classifier>jakarta</classifier>
></dependency>
>```



**Назначение**: Глобальный кэш на уровне `EntityManagerFactory`. 
**~={red}Делится между сессиями, кэширует сущности и коллекции.  =~**

Данные в кэше хранятся в виде Key-Value пар: 
- **Key** - EntityClass, EntityId.
- **Value** - данные сущности, сериализованные в определенный формат (например, двоичный, JSON или XML).

**Настройка** (в `application.yml`):

```yml
spring:
  jpa:
    properties:
      hibernate:
        cache:
          use_second_level_cache: true
          region:
            factory_class: org.hibernate.cache.ehcache.EhCacheRegionFactory

```


Конфигурация ehcache: (ehcache.xml):

Теперь сконфигурируем ehcache.xml. Полное описание всех параметров можно найти в [документации](https://www.ehcache.org/generated/2.10.0/html/ehc-all/index.html#page/Ehcache_Documentation_Set%2Fco-hib_configuring_ehcache_settings.html%23), нам же хватит всего 3 — названия, срока жизни и размера хипа. Для удобства дальнейшего переиспользования зададим параметры в виде шаблона, который потом применим к кэшу MenuItemEntity:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"        xmlns="http://www.ehcache.org/v3"        xsi:schemaLocation="http://www.ehcache.org/v3 http://www.ehcache.org/schema/ehcache-core-3.0.xsd">
	<cache-template name="default">
		<expiry>            
			<ttl unit="days">1</ttl>        
		</expiry>        
		<heap>1000</heap>     
	</cache-template>    
	<cache alias="com.example.cache_for_dummies.entity.MenuItemEntity" uses-template="default"/>
</config>
```

Остался последний штрих: пометить сущ


**Аннотация на сущности**:

```java
@Entity
@Cacheable  // Включает кэширование сущности в L2
@Cache(usage = CacheConcurrencyStrategy.READ_ONLY)  
// Стратегия: READ_ONLY, READ_WRITE, NONSTRICT_READ_WRITE
public class User { ... }
```

Провайдеры: Ehcache, Infinispan, Caffeine. Конфиг в `ehcache.xml`.


**Стратегии использования кэша второго уровня**

Используйте кэш второго уровня для сущностей, которые часто используются и редко изменяются. Это может снизить нагрузку на базу данных и улучшить производительность приложения.

- READ-ONLY — хороша для данных, которые часто читаются, но не изменяются;
    
- READ-WRITE — подходит для приложений, которые регулярно обновляют данные;
    
- NONSTRICT READ-WRITE — подходит, если данные обновляются не так часто, и маловероятно одновременное изменение одной сущности двумя транзакциями;
    
- TRANSACTIONAL — поддержка для транзакционных провайдеров. Используется в JTA-окружении.

#### ~={cyan}To sum it all:=~

Включая кэш второго уровня у вас пропадает возможность балансировать нагрузку (по крайней мере в лоб) между несколькими инстансами приложения, поскольку такие локальные кэши становятся несогласованными друг с другом. Есть у ehcache механизм репликации кэшей, но там свои минусы

И тем не менее, L2 кэш иногда используется — как правильно заметили выше, в основном для неизменяемых справочных сущностей



---
### Query Cache

**Назначение**: Кэширует результаты JPQL/HQL-запросов (не сами сущности).  
**Настройка**: `hibernate.cache.use_query_cache=true` + `setHint("org.hibernate.cacheable", true)` в запросе.  

**Пример**:

```java
Query query = em.createQuery("SELECT u FROM User u WHERE u.status = :status");
query.setHint("org.hibernate.cacheable", true);
query.setParameter("status", Status.ACTIVE);

//или

Query query = em.createQuery("FROM MyEntity WHERE someProperty = :value") .setParameter("value", value)
.setCacheable(true);
```

**Рекомендации по использованию кэша запросов**

- Используйте кэш запросов для часто выполняемых запросов с небольшим количеством вариантов результатов. Это может снизить нагрузку на базу данных и улучшить производительность приложения.

- Не используйте кэш запросов для запросов с большим количеством возможных результатов, так как это может привести к неэффективному использованию памяти и снижению производительности.


---
### Различия с Spring @Cacheable

|Уровень|Область|Аннотации|Что кэширует|
|---|---|---|---|
|Spring Cache|Методы сервисов|@Cacheable|Любые данные (DTO, результаты)|
|JPA/Hibernate L2|Сущности ORM|@Cacheable/@Cache|Сущности, коллекции по ID|
|Query Cache|Запросы|query hints|Результаты SELECT|

**Рекомендация**: L2 для read-mostly сущностей; Spring Cache — для бизнес-логики. Комбинируйте.[​



----
#### [[Кэширование - JPA - Flashcards|Link to flashcards]]



---
### References:

Про конфигурацию L2 cache с Hibernate (no Spring)
https://habr.com/ru/articles/833478/


- [[@Cacheable - Spring Data]]