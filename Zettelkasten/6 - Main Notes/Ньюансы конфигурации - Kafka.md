
15-09-2026 14:29

Status:

Tags: [[Kafka]] [[Java+]]

---
# Ньюансы конфигурации - Kafka


### Автосоздание топиков с default settings


По умолчанию у нас:
```properties
default.replication.factor=1 # Количество реплик по умолчанию при создании topic

auto.create.topics.enable=true
```

И, если Producer присылает сообщение, которое нужно положить в топик, которого еще не существует в Kafka, он будет создан.

НО:
	_Он будет создан с default settings. А значит и replication.factor будет:_
	`default.replication.factor=1`
**~={orange}Это значит, что для таких топиков не будет нормальной репликации.=~** И при падении лидера, запись будет остановлена.


----
#### [[Ньюансы конфигурации - Kafka - Flashcards|Link to flashcards]]



---
### References:

