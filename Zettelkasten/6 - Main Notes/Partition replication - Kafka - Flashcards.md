
Theory for the cards: [[Partition replication - Kafka]]

FILE TAGS: kafka java_interview

Q: Что такое replication factor в Kafka?
A: **Replication factor** — это количество копий (реплик) каждой partition в Kafka topic.​
	
**Зачем нужен**
	
- **1 реплика = нет отказоустойчивости** (leader упал → данные потеряны)
- **3 реплики = выдержит падение 2 брокеров** (1 leader + 2 followers)
<!--ID: 1769950816182-->

Q: Для чего нужны follower partitions в Kafka? Где они обычно располагаются?
A: **Follower partitions нужны в первую очередь для отказоустойчивости и высокой доступности данных, а не для чтения или распределения нагрузки по consumer groups.** Они обеспечивают репликацию данных: если leader partition (единственный брокер, принимающий все чтение/запись для этой partition) упадёт, один из **In-SyncReplicas** автоматически становится новым leader без потери данных, а один из **follower partitions** будет синхронизирован и станет новой ISR.
	
Leader partition располагается на одном брокере (сервере), а followers - на других.
	
```
Partition 0: [Leader на Broker1, Follower на Broker2, Follower на Broker3]
Partition 1: [Leader на Broker2, Follower на Broker3, Follower на Broker1]
Partition 2: [Leader на Broker3, Follower на Broker1, Follower на Broker2]
```
	
**Follower partitions** сами опрашивают лидера: "нет ли у тебя чего-нибудь новенького?" и лидер не ждёт пока они запишут сообщения -> follower partitions могут катастрофически отставать
	
![[Pasted image 20260207105224.png]]
	
**А вот в ISR лидер сам синхронно записывает данные**
<!--ID: 1769932852417-->

Q: Зачем нужны In-Sync replicas в Kafka? В чем их плюсы и минусы? В чём отличия ISR partitions от follower partitions?
A: In-Sync Replicas (ISR) - реплики lead partition, которые имеют ровно то же состояние, что и лидер, т.е. не отстают. При падении лидера выбирается одна из ISR и становится новым лидером.
	
- Leader partition синхронно записывает данные в ISR partitions. 
	`-` понижает скорость записи
	`+` ISR гарантированно синхронизированы
- Если количество реплик упадёт ниже настройки `min.insync.replica`, то мы не сможем записывать сообщения.
	
Рекомендуется иметь не более N-1 реплик ISR, где N - общее число реплик.
	
---
Follower partitions сами считывает изменения лидера. 
	
![[Pasted image 20260207105218.png]]
<!--ID: 1770450790829-->
