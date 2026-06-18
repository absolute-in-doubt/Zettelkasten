
13-03-2026 17:22

Status:

Tags: [[kubectl tool]] [[Kubernetes]] [[CI & CD]]

---
# Все команды - kubectl

## Просмотр ресурсов

```bash
kubectl get pods # список подов в текущем namespace.
    
kubectl get pods --all-namespaces #поды во всех namespace.
    
kubectl get services # список сервисов.
kubectl get svc 
    
kubectl get deployments # список деплойментов.
kubectl get deploy 
    
kubectl describe pod <pod-name> #детальная информация о поде.​
    
kubectl get nodes #список узлов кластера.

kubectl get all -n kubernetes-dashboard
```
    

## Создание и применение

```bash
kubectl apply -f file.yaml # применить YAML-манифест (создать или обновить).
    
kubectl create -f file.yaml # создать из YAML (не обновляет).
    
kubectl create namespace <name> # создать namespace.​
kubectl create ns <name> 
    
kubectl apply -k <dir> # применить Kustomize из директории.
```

## Редактирование и удаление

```bash
kubectl edit pod <pod-name> # открыть манифест в редакторе для правки.
    
kubectl delete pod <pod-name> # удалить под.
    
kubectl delete -f file.yaml # удалить по манифесту.
	
kubectl delete namespace <name> # удалить namespace.
```

## Логи и отладка

```bash
kubectl logs <pod-name> # логи пода.
    
kubectl logs <pod-name> -c <container> # логи конкретного контейнера.
    
kubectl exec -it <pod-name> -- /bin/sh # войти в под как shell.
    
kubectl port-forward <pod-name> 8080:80 # проброс порта.
```    

## Управление контекстом

```bash
kubectl config view # текущая конфигурация.
    
kubectl config current-context # активный контекст.
    
kubectl config use-context <name> # переключить контекст.

kubectl --help # справка по командам.​ 
```



## Полезные флаги

- `-n <namespace>` или `--namespace` — указать namespace.
    
- `-o yaml` или `-o json` — вывод в YAML/JSON.
    
- `--dry-run=client` — тест без применения.
    


----
#### [[Все команды - kubectl - Flashcards|Link to flashcards]]



---
### References:

