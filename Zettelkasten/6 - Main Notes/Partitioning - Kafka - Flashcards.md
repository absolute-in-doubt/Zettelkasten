
Theory for the cards: [[Partitioning & Consumer Groups - Kafka]]

FILE TAGS: kafka java_interview


Q: Какой порядок сообщений получит Consumer из двух partitions?
```
Producer:
1. msg1 (key=user1) → partition_0 (P0)
2. msg2 (key=user2) → partition_1 (P1)  
3. msg3 (key=user1) → partition_0 (P0)
```
A: Неопределённый. msg1→msg3 (P0) сохранят порядок, но msg2 (P1) может прийти в любой момент.
<!--ID: 1769932852435-->

Q: Как в Kafka хранятся оффсеты Consumer-ов? Как достигается их консистентность?
A:   
Они хранятся в отдельном топике `__consumer_offsets`, который реплицируется между брокерами.
	
![[Pasted image 20260207115505.png]]
	
Коммит состоит из 4 частей:
- А - имя топика
- 0 - номер partition - Нет смысла писать имя Consumer-а, т.к. один и тот же partition читается одним и тем же Consumer, однако, если тот падает, Consumer меняется.
- X - имя Consumer Group
- 2 - количество offsets которое надо сдвинуть
	
> **NB!** То есть, для конкретного Consumer offset не хранится, он хранится для конкретной partition, т.к. Consumer может упасть и быть заменённым, а partition останется с тем же номером даже после падения. 
<!--ID: 1770454908556-->

