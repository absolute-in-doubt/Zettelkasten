
14-03-2026 13:07

Status:

Tags: [[Prometheus]] 

---
# PromQL - Prometheus



#### 💡 Basic Examples of PromQL


- `container_cpu_usage_seconds_total`
    Returns all time series with the metric container_cpu_usage_seconds_total
    
- `container_cpu_usage_seconds_total{namespace="kube-system",pod=~"kube-proxy.*"}`
    Returns all time series with the metric `container_cpu_usage_seconds_total` and the given `namespace` and `pod` labels.
    
- `container_cpu_usage_seconds_total{namespace="kube-system",pod=~"kube-proxy.*"}[5m]`
    Returns a whole range of time (in this case 5 minutes up to the query time) for the same vector, making it a range vector.


---
#### ⚙️ Aggregation & Functions in PromQL


- Aggregation in PromQL allows you to combine multiple time series into a single one, based on certain labels.
    
- **Sum Up All CPU Usage**:
    
```shell
sum(rate(node_cpu_seconds_total[5m]))
```
    
    - This query aggregates the CPU usage across all nodes.
- **Average Memory Usage per Namespace:**
    
```shell
avg(container_memory_usage_bytes) by (namespace)
```
    
    - This query provides the average memory usage grouped by namespace.
- **rate() Function:**
    
    - The rate() function calculates the per-second average rate of increase of the time series in a specified range.
    
```shell
rate(container_cpu_usage_seconds_total[5m])
```
    
    - This calculates the rate of CPU usage over 5 minutes.
- **increase() Function:**
    
    - The increase() function returns the increase in a counter over a specified time range.
    
```shell
increase(kube_pod_container_status_restarts_total[1h])
```
    
    - This gives the total increase in container restarts over the last hour.
- **histogram_quantile() Function:**
    
    - The histogram_quantile() function calculates quantiles (e.g., 95th percentile) from histogram data.
    
```shell
histogram_quantile(0.95, sum(rate(apiserver_request_duration_seconds_bucket[5m])) by (le))
```
    
    - This calculates the 95th percentile of Kubernetes API request durations.


----
#### [[PromQL - Prometheus - Flashcards|Link to flashcards]]



---
### References:

