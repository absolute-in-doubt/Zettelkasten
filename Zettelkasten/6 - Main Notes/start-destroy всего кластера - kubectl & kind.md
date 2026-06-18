
04-02-2026 17:47

Status:

Tags: [[kubectl tool]] [[Kubernetes]] [[CI & CD]]

---
# start-destroy всего кластера - Kubernetes

Инфа о кластере:
```bash
kind get clusters # если kind 
kubectl get nodes # просмотреть список нод нашего kind cluster

# ==== Содержимое ноды ============================

# 1. Pods of the kubernetes-dashboard namespace
kubectl get pods -n kubernetes-dashboard 
kubectl describe pods postgres
# 2. Сервисы 
kubectl get svc -n kubernetes-dashboard 
# 3. Все компоненты Dashboard 
kubectl get all -n kubernetes-dashboard

kubectl config current-context # покажет текущий контекст
```


### Завершение кластера

```bash
# Полное удаление кластера
kind delete cluster --name test-cluster  # или просто kind delete cluster
kubectl delete pod postgres

# Если несколько кластеров
kind delete clusters --all

# ====== Сохранение всех файлов манифеста текущей конфигурации ========

# Экспорт всех ресурсов в YAML (в текущий namespace или все)
kubectl get all --all-namespaces -o yaml > cluster-state.yaml

# Или по типам:
kubectl get deployments,services,configmaps,secrets,pods -A -o yaml > resources.yaml

# PersistentVolumes (если есть)
kubectl get pv,pvc -o yaml > storage.yaml

```


### Создание кластера:

```bash
# ==== Если вынесли все .yaml файлы для каждого компонента: ========

kind create cluster --name mycluster
kubectl apply -f before-delete.yaml  # восстановит ВСЕ ресурсы

# ==== Если есть файл конфигурации всего кластера: =================

kind create cluster --config cluster-config.yaml
kind create cluster --name observability --config ./observability-cluster-config.yml
```


----
#### [[start-destroy всего кластера - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

