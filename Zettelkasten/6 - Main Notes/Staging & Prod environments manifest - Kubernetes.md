
04-02-2026 19:52

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Staging & Prod environments manifest - Kubernetes

### Staging

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: app-staging
  labels:
    environment: staging
    team: backend
  annotations:
    description: "Staging - prod-like configuration"
    prometheus.io/scrape: "true"    # ✅ Мониторинг как в prod
---
apiVersion: v1
kind: ResourceQuota
metadata:
  name: staging-quota
  namespace: app-staging
spec:
  hard:
    pods: "10"             # ↓ Меньше подов (ближе к prod)
    requests.cpu: "8"      # → Ресурсы как в prod
    limits.cpu: "16"
---
apiVersion: v1
kind: LimitRange
metadata:
  name: staging-limits
  namespace: app-staging
spec:
  limits:
  - type: Container
    default:              # → Те же лимиты что в prod
      cpu: "500m"
      memory: "512Mi"

```



#### Prod

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: app-prod
  labels:
    environment: production
    team: backend
    compliance: pci-dss        # Регуляторные метки
  annotations:
    description: "Production - strict security"
    prometheus.io/scrape: "true"
    networkpolicy.k8s.io/ignore: "false"  # ✅ NetworkPolicy включены
---
apiVersion: v1
kind: ResourceQuota
metadata:
  name: prod-quota
  namespace: app-prod
spec:
  hard:
    pods: "5"              # ↓ Минимум подов (HA через replicas)
    requests.cpu: "16"     # → Больше гарантированных ресурсов
    limits.cpu: "24"
    pvcStorageRequestGi: "100Gi"  # ✅ Persistent storage
---
apiVersion: v1
kind: LimitRange
metadata:
  name: prod-limits
  namespace: app-prod
spec:
  limits:
  - type: Container
    max:                  # ↓ Строгие лимиты (защита от OOM)
      cpu: "2"
      memory: "2Gi"
    min:                  # ↑ Минимальные требования
      cpu: "250m"
      memory: "128Mi"
```

|**Параметр**|**DEV**|**STAGING**|**PROD**|
|---|---|---|---|
|**Pods quota**|20+|10|5|
|**CPU limits**|4+|Prod-like|Строгие (2/core)|
|**Memory limits**|4Gi+|Prod-like|2Gi/core|
|**Мониторинг**|❌|✅ Prometheus|✅ + alerting|
|**NetworkPolicy**|❌|⚠️|✅ Обязательно|
|**Finalizers**|❌|❌|✅ Compliance hooks|
|**Labels**|`dev`|`staging`|`production, compliance`|
