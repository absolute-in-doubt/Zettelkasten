
04-02-2026 19:19

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Namespaces - Kubernetes

Namespaces — это **логическая абстракция** (виртуальные кластеры) внутри **control-plane** Kubernetes, которая разделяет ресурсы по всей инфраструктуре.

```
Физический кластер (1+ нод)
    ↓
Control-plane (API Server, etcd)
    ↓  
Логическое разделение:
- default (ваши тестовые Pod-ы)
- kube-system (core компоненты)  
- kubernetes-dashboard (Dashboard)
- dev, prod, team1...
```


### Где физически хранится

```
API Server → etcd (база данных кластера)
    ↓
Метка "namespace: kubernetes-dashboard" 
    ↓
Pod → размещается на ЛЮБОЙ ноде кластера
```


### Как работает разделение

```
1 нода (kind-control-plane)
├── kube-system (coredns, metrics-server)
├── default (ваши приложения)  
└── kubernetes-dashboard (Dashboard Pod)
```


### Стандартные namespaces

```
default          ← ваши тесты по умолчанию
kube-system      ← core компоненты (coredns, scheduler)
kube-public      ← публичные ресурсы
kube-node-lease  ← lease объекты
kubernetes-dashboard ← Dashboard
```



---
### Команды для работы с namespaces

```bash
# Все namespace-ы кластера
kubectl get namespaces

# Поды по namespace-ам (на всех нодах)
kubectl get pods --all-namespaces

# Конкретный Pod и его нода
kubectl get pods -n kubernetes-dashboard -o wide 
```


### Создание namespace

1. Императивное
```bash
kubectl create namespace my-app
```

2. Декларативное - через yaml.
```bash
kubectl apply -f namespace-config.yaml
```


Пример манифеста для namespace:

```yaml
apiVersion: v1                    # Версия API Kubernetes (для Namespace всегда v1)
kind: Namespace                   # Тип ресурса - пространство имен

metadata:
  name: my-app                    # Обязательное: уникальное имя namespace (только lowercase, цифры, дефисы)
  
  # Labels - ключевые метки для поиска/фильтрации/группировки
  labels:
    app.kubernetes.io/name: my-app        # Стандартная схема label для названия приложения
    app.kubernetes.io/instance: prod-001  # Уникальный инстанс (окружение/версия)
    app.kubernetes.io/version: "1.2.3"    # Версия приложения
    app.kubernetes.io/component: backend  # Компонент (frontend/backend/database)
    environment: production               # Пользовательская метка (окружение)
    team: backend-team                    # Владелец/команда
    
  # Annotations - произвольные метки (не влияют на выборку подов)
  annotations:
    # Описание для людей
    description: "Production namespace для backend сервисов"
    
    # Интеграция с внешними системами
    prometheus.io/scrape: "true"           # Включить мониторинг Prometheus
    prometheus.io/port: "8080"             # Порт для метрик
    
    # Политики безопасности (NetworkPolicy, ResourceQuota)
    kubectl.kubernetes.io/last-applied-configuration: ""  # Сохранение декларативного состояния
    
# spec - желаемое состояние namespace
spec:
  # Finalizers - "хуки" перед удалением (блокируют каскадное удаление)
  finalizers:
    - kubernetes                           # Стандартный финализатор K8s

# =============================================================
# Дополнительно: ResourceQuota (лимит ресурсов в namespace)
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-resources              # Имя квоты
  namespace: my-app                    # Привязка к namespace
spec:
  hard:
    requests.cpu: "4"                  # Лимит CPU запросов всех подов
    requests.memory: 8Gi               # Лимит памяти запросов
    limits.cpu: "8"                    # Лимит CPU лимитов
    limits.memory: 16Gi                # Лимит памяти лимитов
    pods: "50"                         # Макс. количество подов
    
# =============================================================
# Дополнительно: LimitRange (по умолчанию для контейнеров)
apiVersion: v1
kind: LimitRange
metadata:
  name: default-limits
  namespace: my-app
spec:
  limits:
  - type: Container                    # Применяется к контейнерам
    default:                           # Значения по умолчанию (если не указаны)
      cpu: 500m
      memory: 256Mi
    defaultRequest:                    # Запросы по умолчанию
      cpu: 200m
      memory: 128Mi
    max:                               # Максимум для контейнера
      cpu: 2
      memory: 1Gi
    min:                               # Минимум для контейнера
      cpu: 100m
      memory: 64Mi

```


```bash
# Фильтрация по меткам
kubectl get ns -l environment=production

# Развертывание в namespace
kubectl apply -f app.yaml -n my-app

# Мониторинг ресурсов
kubectl top pods -n my-app
kubectl describe resourcequota compute-resources -n my-app
```


### Удаление namespaces

```bash
kubectl delete namespace my-app
```

> [!warning]  ⚠️ **ВАЖНО: Удаление каскадное**
> **Удаляет ВСЕ ресурсы внутри** (Pod, Service, ConfigMap, Secrets и т.д.).
> 


----
#### [[Namespaces - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

