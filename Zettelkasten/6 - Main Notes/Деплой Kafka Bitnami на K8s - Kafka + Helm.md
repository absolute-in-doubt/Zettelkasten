
18-03-2026 11:05

Status:

Tags: [[Kafka]] [[Helm chart]] [[Java+]]

---
# Деплой Kafka Bitnami на K8s - Kafka + Helm


```shell
# Kafka с KRaft (kraft.enabled=true)
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install kafka bitnami/kafka \
  --set kraft.enabled=true \
  --set controller.replicaCount=3 \
  --set broker.replicaCount=3 \
  --namespace kafka --create-namespace

# Schema Registry (отдельный chart)
helm install schema-registry bitnami/schema-registry \
  --set kafka.externalBrokers[0]=PLAINTEXT://kafka:9092 \
  --namespace kafka
```

---
## Регистрация схем через REST API

**Port-forward для доступа**:

```shell
kubectl -n kafka port-forward svc/schema-registry 8081:8081
```

**Загрузить схему**:

> [!note] 
> **Schema Registry ожидает JSON объект** с полем `schema`, где значение — **строка с Avro схемой**.

```shell
curl -X POST- H "Content-Type: application/vnd.schemaregistry.v1+json" \
  --data '{
    "schema": "{\"type\": \"record\", \"name\": \"User\", \"fields\": [{\"name\": \"id\", \"type\": \"string\"}, {\"name\": \"name\", \"type\": \"string\"}]}"
  }' http://localhost:8081/subjects/user-value/versions
```

**Проверить**:

```shell
curl http://localhost:8081/subjects
curl http://localhost:8081/subjects/user-value/versions/latest
```



---
### References:

