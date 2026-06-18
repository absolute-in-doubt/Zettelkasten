
14-03-2026 10:11

Status:

Tags: [[kubectl tool]] [[Kubernetes]] [[CI & CD]]

---
# Работа с несколькими кластерами - kubectl & helm

В kubectl кластер указывается через **контекст** в файле kubeconfig (~/.kube/config). Все команды применяются к текущему активному контексту, который можно переключать несколькими способами.

#### Просмотр доступных кластеров

```bash
# Список всех контекстов (кластеров)
kubectl config get-contexts

# Текущий активный контекст
kubectl config current-context
```


---
### Способы указания кластера

#### 1. Переключение контекста (рекомендуется)

```bash
kubectl config use-context observability  # Переключить на кластер observability

kubectl config use-context kind-observability  # kind кластер из предыдущего диалога
```

#### 2. Указание флага --context в каждой команде


```bash
kubectl get pods -n monitoring --context=observability

kubectl get nodes --context=kind-observability
```


#### 3. Указание полного пути к kubeconfig

```bash
kubectl get pods --kubeconfig=/path/to/eks-config  # EKS кластер

kubectl get pods --kubeconfig=$HOME/.kube/kind-config  # kind
```


---
## Пример

```bash
# 1. Проверить доступные кластеры
kubectl config get-contexts
# CURRENT   NAME                   CLUSTER              AUTHINFO             NAMESPACE
#           kind-observability     kind-observability   kind-observability   
# *         observability          observability        observability        

# 2. Переключиться на kind кластер
kubectl config use-context kind-observability

# 3. Проверить - должны быть видны kind ноды
kubectl get nodes

# 4. Установить Prometheus именно на kind
helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring --create-namespace 

```
​



----
#### [[Работа с несколькими кластерами - kubectl & helm - Flashcards|Link to flashcards]]



---
### References:

