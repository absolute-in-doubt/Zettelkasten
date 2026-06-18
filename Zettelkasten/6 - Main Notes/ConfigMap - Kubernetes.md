
18-03-2026 11:52

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# ConfigMap - Kubernetes

**ConfigMap** — это объект K8s для хранения **неконфиденциальных** настроек в формате **ключ=значение**. Он отделяет конфигурацию от Docker образа, позволяя менять настройки **без пересборки приложения**.


### **Жизненный цикл**:

```
ConfigMap → Deployment/Pod → ENV vars / Файлы → Spring Boot читает
     ↓
kubectl edit configmap → новые поды получают изменения
```


#### Способы передачи ConfigMap в Pod (чтение ConfigMap в приложении)

| Способ        | В Pod                 | Применение                              |
| ------------- | --------------------- | --------------------------------------- |
| `env`         | Переменные окружения  | Spring `@Value`, `System.getenv()`      |
| `envFrom`     | **Все ключи** → ENV   | **Spring Boot** (рекомендуется)         |
| `volumeMount` | Файлы в `/etc/config` | `application.properties` - замена файла |


---
### Пример ConfigMap

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kafka-app-config
  namespace: kafka-app
data:
  # ENV переменные (префикс для Spring: KAFKA_, SPRING_KAFKA_)
  KAFKA_BOOTSTRAP_SERVERS: "kafka.kafka.svc.cluster.local:9092"
  SPRING_KAFKA_PROPERTIES_SCHEMA_REGISTRY_URL: "http://schema-registry.kafka.svc:8081"
  SPRING_KAFKA_CONSUMER_GROUP_ID: "user-group"
  SPRING_KAFKA_PRODUCER_KEY_SERIALIZER: "org.apache.kafka.common.serialization.StringSerializer"
  SPRING_KAFKA_PRODUCER_VALUE_SERIALIZER: "io.confluent.kafka.serializers.KafkaAvroSerializer"
  SPRING_KAFKA_CONSUMER_VALUE_DESERIALIZER: "io.confluent.kafka.serializers.KafkaAvroDeserializer"
  SPRING_KAFKA_PROPERTIES_SPECIFIC_AVRO_READER: "true"
  
  # Логгинг
  LOGGING_LEVEL_COM_EXAMPLE: "INFO"
  SPRING_PROFILES_ACTIVE: "kubernetes"
```


#####  **Deployment с envFrom (самый простой)**

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kafka-producer-app
  namespace: kafka-app
spec:
  replicas: 1
  template:
    spec:
      containers:
      - name: app
        image: my-spring-app:1.0
        envFrom:                    # ← ВСЕ ключи ConfigMap → ENV
        - configMapRef:
            name: kafka-app-config
        ports:
        - containerPort: 8080
```


> [!tip]
> Spring считает данные из ConfigMap как ENV.
> Можно их установить из application.yml, например так:
> ```yml
> minio:  
  base-url: ${MINIO_BASE_URL:http://localhost:9000}  
  bucket-name: ${MINIO_BUCKET_NAME:fitness-tracker}  
  access-key: ${MINIO_ACCESS_KEY:minioadmin}  
  secret-key: ${MINIO_SECRET_KEY:minioadmin}
> ```
> Или же инжектить через **~={purple}@Value=~** или Environment. Но в этом случае не указать fallback values для локальных тестов. 



----
#### [[ConfigMap - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

