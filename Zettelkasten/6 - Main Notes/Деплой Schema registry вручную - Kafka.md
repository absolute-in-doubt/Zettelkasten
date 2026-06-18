
18-03-2026 10:38

Status:

Tags: [[Kafka]] [[Java+]]

---
# Деплой Schema registry - Kafka


**Schema Registry в Kafka** — **~={orange}это отдельный сервис=~**, не являющийся частью брокера Kafka. Он деплоится как standalone-приложение (REST API на порту 8081 по умолчанию), взаимодействующее с Kafka-кластером для хранения схем в специальной топике `_schemas` (compacted topic).

## Деплой Schema Registry

Развертывается как несколько инстансов (рекомендуется 3+ для HA) на отдельных машинах или контейнерах (Docker/Kubernetes). Каждый инстанс:

- Подключается к Kafka через `kafkastore.bootstrap.servers`.
    
- Хранит схемы в топике `_schemas` (с replication factor 3, partitions 1, cleanup.policy=compact).
    
- Выбирает primary-лидера через Kafka (не ZooKeeper с версии 7.0+).
    

---

### **Передача схем через ConfigMap** (регистрируются автоматически при старте):

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: schema-registry-schemas
  namespace: kafka
data:
  user-value.avsc: |
    {
      "type": "record",
      "name": "User",
      "fields": [
        {"name": "id", "type": "string"},
        {"name": "name", "type": "string"}
      ]
    }
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: schema-registry-config
  namespace: kafka
data:
  schema-registry.properties: |
    kafkastore.bootstrap.servers=kafka:9092
    listeners=http://0.0.0.0:8081
    kafkastore.topic=_schemas
    kafkastore.topic.replication.factor=3
    schema.compatibility.level=BACKWARD
```


---
### K8s манифест

```yml
apiVersion: v1
kind: Service                    # Создаёт внутренний сервис
metadata:
  name: schema-registry          # Имя сервиса (schema-registry.kafka.svc)
  namespace: kafka               # Namespace кластера
  labels:
    app: schema-registry         # Метка для поиска подов
spec:
  type: ClusterIP                 # Только внутри кластера (не LoadBalancer)
  ports:
  - port: 8081                    # Порт сервиса
    targetPort: 8081             # Порт пода
    name: http                   # Имя порта
  selector:                       # К каким подам привязать
    app: schema-registry
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: schema-registry
  namespace: kafka
spec:
  replicas: 3                      # 3 копии для HA (high availability)
  selector:
    matchLabels:
	app: schema-registry           # Deployment управляет подами с этой меткой 
template:                          # Шаблон пода
    metadata:
      labels:
        app: schema-registry       # Service находит поды по этой метке
    spec:
      containers:
      - name: schema-registry
        image: confluentinc/cp-schema-registry:7.7.0
        ports:
        - containerPort: 8081
        env:
        - name: SCHEMA_REGISTRY_HOST_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name  # Имя пода (sr-abc-123) для leader election
        - name: SCHEMA_REGISTRY_KAFKASTORE_BOOTSTRAP_SERVERS
          value: "kafka-0.kafka:9092,kafka-1.kafka:9092,kafka-2.kafka:9092"    # Адреса Kafka брокеров для хранения схем
        - name: SCHEMA_REGISTRY_LISTENERS
          value: "http://0.0.0.0:8081"     # На каком адресе слушать HTTP API
        volumeMounts:
        - name: config                     # Монтирует schema-registry.properties
          mountPath: /etc/schema-registry
        - name: schemas                    # Монтирует .avsc файлы схем
          mountPath: /etc/schema-registry/schemas
        resources:
          requests:
            memory: "1Gi"
            cpu: "500m"
          limits:
            memory: "2Gi"
            cpu: "1"
        livenessProbe:                   # Перезапуск пода если не отвечает
			httpGet:
			    path: /                  # Корень Schema Registry API
			    port: 8081
			initialDelaySeconds: 30      # Ждать 30с после старта
			periodSeconds: 30            # Проверять каждые 30с
		readinessProbe:                  # Под готов к трафику?
			httpGet:
			    path: /subjects          # Список тем (проверка работоспособности)
			    port: 8081
		  initialDelaySeconds: 20
		  periodSeconds: 10
      volumes:
      - name: config
        configMap:                        # Берет из ConfigMap
          name: schema-registry-config
          items:
          - key: schema-registry.properties
            path: schema-registry.properties
      - name: schemas
        configMap:                        # Берет из ConfigMap
          name: schema-registry-schemas
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding                 # Связывает ServiceAccount с Role
metadata:
  name: schema-registry
  namespace: kafka
subjects:
- kind: ServiceAccount            # default SA пода
  name: default
  namespace: kafka
roleRef:
  kind: Role                      # Kafka permissions
  name: kafka-broker              # Role с доступом к Kafka топикам
  apiGroup: rbac.authorization.k8s.io
```

Ещё полезные ENV переменные:

- `SCHEMA_REGISTRY_SCHEMA_COMPATIBILITY_LEVEL=BACKWARD|NONE|FULL`
    
- `SCHEMA_REGISTRY_KAFKASTORE_TOPIC_REPLICATION_FACTOR=3`
    
- `SCHEMA_REGISTRY_MASTER_ELIGIBILITY=true` (для leader election)


##### Деплой схемы

```shell
kubectl apply -f schema-registry.yaml -n kafka
kubectl wait --for=condition=ready pod -l app=schema-registry --timeout=300s -n kafka

# Проверка
curl -X POST http://schema-registry.kafka.svc.cluster.local:8081/subjects/test-value/versions \
  -H "Content-Type: application/vnd.schemaregistry.v1+json" \
  --data '{"schema": "{\"type\":\"string\"}"}'

# Список схем
curl http://schema-registry.kafka.svc.cluster.local:8081/subjects
```










---


~={pink}**Какую схему данных использует Kafka, если Schema registry незадеплоен?**=~

Kafka без Schema Registry не имеет встроенной схемы данных или эволюции схем — это схемолесс (schemaless) система. Брокеры хранят сырые байты сообщений, а схема (структура) определяется только на стороне producer/consumer через сериализаторы.
	
#### Сериализация без Registry
	
По умолчанию используется `StringSerializer`/`StringDeserializer` (UTF-8 строки) или `ByteArraySerializer` (байты). Для структурированных данных (JSON, Avro, Protobuf) применяют:
	
- **JSON**: `JsonSerializer` (с Jackson) — сериализует объект в JSON-строку без контроля версий.
    
- **Avro**: Ручная сериализация с `SpecificDatumWriter` и schema в коде; embed schema в каждое сообщение (overhead) или используйте `ByteArraySerializer` + GenericAvroCodecs.​
    
- **Protobuf**: Аналогично, через Google Protobuf сериализаторы.
​
#### Минусы подхода

- Нет централизованного хранения/версионирования схем.
    
- Изменения схемы требуют обновления всех producer/consumer (риск несовместимости).
    
- Больший размер сообщений (embedded schema для Avro ~килобайты).
    

Schema Registry решает это, храня схемы с ID (5 байт overhead) и проверкой совместимости.

----
#### [[Деплой Schema registry - Kafka - Flashcards|Link to flashcards]]



---
### References:

