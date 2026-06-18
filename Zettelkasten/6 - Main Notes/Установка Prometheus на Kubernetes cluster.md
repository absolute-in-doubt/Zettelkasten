
14-03-2026 11:36

Status:

Tags: [[Prometheus]]

---
# Установка Prometheus на кластер

```bash
# здесь - развертываем в кластере "observability"
kind create cluster --name observability --config ./observability-cluster-config.yml # тут указываем настройки нод (2 worker nodes), cluster api version, control plane settings

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# используем нужный кластер
kubectl config use-context kind-observability


# 4. Установить Prometheus именно на kind
helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring --create-namespace 

# проверка
kubectl get all -n monitoring
```


#### Проброс портов

```bash
# Prometheus UI: http://localhost:9090
kubectl port-forward service/prometheus-operated -n monitoring 9090:9090

# Grafana UI: http://localhost:8080
kubectl port-forward service/monitoring-grafana -n monitoring 8080:80

# Alertmanager UI: http://localhost:9093
kubectl port-forward service/alertmanager-operated -n monitoring 9093:9093
```


### Удаление и очистка


- **Uninstall with helm chart**:

```shell
helm uninstall monitoring --namespace monitoring
```

- **Delete namespace**:

```shell
kubectl delete ns monitoring
```

- **Delete Cluster & everything else**:

```shell
kind delete cluster --name observability
```


----
#### [[Установка Prometheus на кластер - Flashcards|Link to flashcards]]



---
### References:

