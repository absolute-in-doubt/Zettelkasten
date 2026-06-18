
18-03-2026 12:30

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Хранение манифестов в Git - Kubernetes

```
company-infra/
├── clusters/                 # Кластеры
│   ├── prod-eu-west/
│   │   └── cluster.yaml
│   └── staging/
├── infra/                     # Инфраструктура
│   └── kafka/
│       ├── 01-namespace.yaml
│       ├── 02-networkpolicy.yaml
│       ├── 03-resourcequota.yaml
│       ├── 04-rbac.yaml         # ServiceAccount + RoleBinding
│       ├── 05-kafka.yaml        # chart
│       ├─- kustomization.yaml # Сборка всего
│       └── 99-cleanup.yaml
├── apps/
│   └── kafka-producer/
│       ├── Chart.yaml       # Helm chart
│       ├── values-prod.yaml
│       └── templates/
└── envs/                     # Окружения
    ├── prod/
    └── staging
```



---
### References:

