
14-03-2026 13:06

Status:

Tags: [[Prometheus]]

---
# Metrics - Prometheus

- Metrics are paired with Labels.
- Labels are key-value pairs that allow you to differentiate between dimensions of a metric, such as different services, instances, or endpoints.

#### 🔍 Example:


```shell
container_cpu_usage_seconds_total{namespace="kube-system", endpoint="https-metrics"}
```

- `container_cpu_usage_seconds_total` is the metric.
- `{namespace="kube-system", endpoint="https-metrics"}` are the labels.


### Metric types


#### Counter

Всегда инкрементируется:
- `number_of_logins` - custom metric
- `kube_pod_container_status_restarts_total`

---
#### Gauge

Может как инкрементироваться, так и декрементироваться:
- `instance:node_cpu_utilisation:rate5m`

---
#### Histogram

Для отображения значений по группам. Например, группировка задержки http_requests: 5ms, 10ms, .... И в bucket 0 будут запросы с задержкой от 0ms до 5 ms.

- **Metric Example**: `apiserver_request_duration_seconds_bucket`

---
#### Summary

- Similar to a Histogram, a Summary samples observations and provides a total count of observations, their sum, and configurable quantiles (percentiles).
- **Example**: Monitoring the 95th percentile of request durations to understand high latency in your Kubernetes API.
- **Metric Example**: `apiserver_request_duration_seconds_sum`

---


## Пример создания custom метрик в приложении  (JS):

Т.е. они должны быть одного из четырёх типов и выполнять constraints этого типа. И возвращать их значения будем при запросе на endpoint `/metrics`.

```javascript
// Prometheus metrics
const httpRequestCounter = new promClient.Counter({
    name: 'http_requests_total',
    help: 'Total number of HTTP requests',
    labelNames: ['method', 'path', 'status_code'],
});

  

const requestDurationHistogram = new promClient.Histogram({
    name: 'http_request_duration_seconds',
    help: 'Duration of HTTP requests in seconds',
    labelNames: ['method', 'path', 'status_code'],
    buckets: [0.1, 0.5, 1, 5, 10], // Buckets for the histogram in seconds
});

  

const requestDurationSummary = new promClient.Summary({
    name: 'http_request_duration_summary_seconds',
    help: 'Summary of the duration of HTTP requests in seconds',
    labelNames: ['method', 'path', 'status_code'],
    percentiles: [0.5, 0.9, 0.99], // Define your percentiles here
});

  
  
  

// Gauge metric
const gauge = new promClient.Gauge({
    name: 'node_gauge_example',
    help: 'Example of a gauge tracking async task duration',
    labelNames: ['method', 'status']
});
```



---
### References:

