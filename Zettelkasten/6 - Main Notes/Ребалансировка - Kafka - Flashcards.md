
Theory for the cards: [[Ребалансировка - Kafka]]

FILE TAGS: kafka

Q: Как Kafka понимает, что Consumer умер?
A: У каждого Consumer есть heartbeat - Kafka периодически пингует Consumer-ов. Если Consumer престал отвечать на запросы - считается, что он умер.
	
Настраивается через 
- `heartbeat.interval.ms` - настраивает то, с каким интервалом Consumer-ы будут опрашиваться.
- `session.timeout.ms` - через какое время после первого неотвеченного heartbeat произойдет ребалансировка. Даёт время на дополнительные попытки heartbeat-запросов.
<!--ID: 1770567039121-->
