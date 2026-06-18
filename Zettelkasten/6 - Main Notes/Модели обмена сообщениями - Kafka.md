
01-02-2026 13:00

Status:

Tags: [[Kafka]] [[Java+]]

---
# Модели обмена сообщениями - Kafka


### Как отправляем (Producer)

Отправлять стоит в топики без определённого ключа, чтобы Kafka сама выбирала любой из partitions по своим алгоритмам.

**Пример:**

1 Producer
3 Consumers

Если отправлять с определённым ключом, то два Consumer будут всегда простаивать, т.к. сообщение всегда будет попадать в один и тот же partition. -> используем отправку в топик без ключа.

> [!tip]
> Если работаем с пользователями - как ключ логично использовать `session_id`. -> Все действия пользователя упорадочены в одной partition.
>
>Если цель - аналитика однотипных действий - можно по action_type. -> Все однотипные действия хранятся по порядку в одном partition.


**Kafka хранит offsets для Consumer Group в специальном внутреннем топике [[Partitioning & Consumer Groups - Kafka#Consumer groups|__consumer_offsets]]**.


---
### Point-to-Point (Очередь)

![[Pasted image 20260201132144.png]]

```
Producer → Topic → Consumer Group "order-queue" (1 Consumer)
└── Consumer1 читает P0+P1 последовательно
```

- **Offset**: **Уникальный** для Consumer (так как **группа = 1 Consumer**)
    
- **Логика**: **Один** Consumer обрабатывает **все** сообщения топика
    
- **Пример**: Обработка заказов, заданий


---
### Publisher - Subscriber 


![[Pasted image 20260201132123.png]]
```
Producer → Topic → Consumer Group "news-processors"
├── Consumer1 читает P0 (offset=100)
├── Consumer2 читает P1 (offset=50)  
└── Consumer3 читает P2 (offset=75)
```

- **Offset**: Общий для группы по partition (`group_id` + `topic` + `partition`) - для каждого Consumer - свой offset. 
    
- **Логика**: Каждый Consumer в группе видит только те сообщения, которые попали в его partition, который он читает.
    
- **Пример**: Обработка новостей, событий, логов


---
### Direct Assignment (Ручное назначение)

```java
consumer.assign([new TopicPartition("orders", 0)]);  // Только P0
```

- **Offset**: **Уникальный** для каждого Consumer (игнорирует group_id)
    
- **Логика**: Consumer сам выбирает partitions, нет координации
    
- **Пример**: Backview исторических данных, мониторинг


----
#### [[Модели обмена сообщениями - Kafka - Flashcards|Link to flashcards]]



---
### References:

