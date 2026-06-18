
Theory for the cards: [[Prometheus architecture overview]]

FILE TAGS: observability java_middle_interview

Q: Опиши шаги, которые нужно проделать, чтобы метрики нашего приложения стали доступны в Prometheus.
A:  
1. Создать endpoint `/metrics`, который будет возвращать метрики в виде, который может прочитать Prometheus:
	- Counter - только инкрементируемое значение
	- Gauge - инкрементируемое и декрементируемое значение
	- Histogram - набор значениё разбитых по диапазонам ("buckets")
	- Summary - набор значений, разбитых по процентилям (95%  процентиль времени обработки http запроса)
2. Запустить на кластере Prometheus
3. Запустить service discovery:
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
<!--ID: 1773502706269-->
