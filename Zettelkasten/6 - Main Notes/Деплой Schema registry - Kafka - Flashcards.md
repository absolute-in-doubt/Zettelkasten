
Theory for the cards: [[Деплой Schema registry вручную - Kafka]]

FILE TAGS: kafka

Q: Какую схему данных использует Kafka, если Schema registry незадеплоен?
A: Kafka без Schema Registry не имеет встроенной схемы данных или эволюции схем — это схемолесс (schemaless) система. Брокеры хранят сырые байты сообщений, а схема (структура) определяется только на стороне producer/consumer через сериализаторы.
	
**Сериализация без Registry**
	
По умолчанию используется `StringSerializer`/`StringDeserializer` (UTF-8 строки) или `ByteArraySerializer` (байты). Для структурированных данных (JSON, Avro, Protobuf) применяют:
	
- **JSON**: `JsonSerializer` (с Jackson) — сериализует объект в JSON-строку без контроля версий.
    
- **Avro**: Ручная сериализация с `SpecificDatumWriter` и schema в коде; embed schema в каждое сообщение (overhead) или используйте `ByteArraySerializer` + GenericAvroCodecs.​
    
- **Protobuf**: Аналогично, через Google Protobuf сериализаторы.
	​
---
**Минусы подхода**
	
- Нет централизованного хранения/версионирования схем.
    
- Изменения схемы требуют обновления всех producer/consumer (риск несовместимости).
    
- Больший размер сообщений (embedded schema для Avro ~килобайты).
    
Schema Registry решает это, храня схемы с ID (5 байт overhead) и проверкой совместимости.
<!--ID: 1773819893152-->


