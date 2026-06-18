
04-02-2026 19:54

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Dev environment manifest - Kubernetes

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: app-dev
  labels:
    environment: dev
    team: backend
  annotations:
    description: "Development namespace - unlimited resources"
---
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-quota
  namespace: app-dev
spec:
  hard:
    pods: "20"           # ↑ Много подов для тестов
    requests.cpu: "4"    # ↑ Больше CPU для экспериментов  
    limits.cpu: "8"      # ↑ Нет жестких лимитов
---
apiVersion: v1
kind: LimitRange
metadata:
  name: dev-limits
  namespace: app-dev
spec:
  limits:
  - type: Container
    max:                 # ↑ Щедрые лимиты
      cpu: "4"
      memory: "4Gi"
    min:                 # ↓ Можно запускать "прожорливые" поды
      cpu: "10m"
      memory: "32Mi"
```