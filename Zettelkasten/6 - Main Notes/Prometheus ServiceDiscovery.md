
14-03-2026 18:39

Status:

Tags: [[Prometheus]]

---
# Prometheus ServiceDiscovery


**Prometheus Service Discovery (SD)** — механизм автоматического обнаружения целей мониторинга без статических IP-адресов. Prometheus периодически опрашивает источники (K8s API, Consul, DNS) и обновляет список targets.

## Принцип работы

```
Prometheus → Service Discovery → Targets (/metrics) → Scraping
           | 	               |                    |
      читает config      опрашивает K8s API     HTTP GET /metrics
```

1. **Читает `scrape_configs`** из `prometheus.yml`
    
2. **SD опрашивает** K8s API (поды, сервисы, endpoints)
    
3. **Relabeling** фильтрует/трансформирует метки
    
4. **Scraping** каждые `scrape_interval` секунд
    

## Kubernetes SD роли

|Роль|Источник|Пример использования|
|---|---|---|
|`node`|/api/v1/nodes|Node Exporter|
|`pod`|/api/v1/pods|App metrics|
|`service`|/api/v1/services|Service endpoints|
|`endpoints`|/api/v1/endpoints|Custom endpoints|
|`ingress`|/apis/networking.k8s.io/v1/ingresses|Ingress controller|

#### Базовый пример prometheus.yml

```yml
global:
  scrape_interval: 15s

scrape_configs:
# 1. Node Exporter (все ноды)
- job_name: 'kubernetes-nodes'
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    regex: '(.*):10250'
    replacement: '${1}:9100'  # Node Exporter порт

# 2. Поды с аннотацией prometheus.io/scrape: 'true'
- job_name: 'kubernetes-pods'
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  # Только поды с аннотацией
  - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
    action: keep
    regex: true
  # Заменяем адрес на pod_ip:port
  - source_labels: [__meta_kubernetes_pod_ip, __meta_kubernetes_pod_annotation_prometheus_io_port]
    action: replace
    regex: (.+);(.+)
    replacement: $1:$2
    target_label: __address__
  # Копируем метки
  - action: labelmap
    regex: __meta_kubernetes_pod_label_(.+)

# 3. Services
- job_name: 'kubernetes-services'
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape]
    action: keep
    regex: true
```

> [!note]
> Обычно достаточно просто задеплоить ServiceMonitor, который будет указывать на нужжное приложение. 
> ~={cyan}**ServiceMonitor автоматически генерирует scrape_config!**=~


---
## Prometheus Operator (ServiceMonitor)


```yml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  labels:
    app: a-service-service-monitor
    release: monitoring
  name: a-service-service-monitor
  namespace: monitoring
spec:
  jobLabel: job
  endpoints:
    - interval: 2s
      port: a-service-port
      path: /metrics
  selector:
    matchLabels:
      app: a-service
  namespaceSelector:
    matchNames:
      - dev
```

~={cyan}**Автоматически генерирует scrape_config!**=~



----
#### [[Prometheus ServiceDiscovery - Flashcards|Link to flashcards]]



---
### References:

