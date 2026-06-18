
05-02-2026 09:34

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Deployment - Kubernetes


Deployment в Kubernetes — это контроллер, который управляет жизненным циклом набора Pod'ов, обеспечивая желаемое состояние приложения (количество реплик, обновления, откаты). Он создаёт ReplicaSet для автоматического масштабирования и восстановления Pod'ов при сбоях.

## Отличия от Pod/Service manifest

- **Deployment vs манифесты**: Обычный Pod/Service-манифест статичен (kubectl apply разово), Deployment декларативен — Kubernetes непрерывно поддерживает состояние, обновляя Pod'ы rolling-стратегией без downtime.



### Манифест

```yaml
apiVersion: apps/v1
kind: Deployment # использует kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3 # amt of replicas
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
  strategy: 
    type: RollingUpdate # Kubernetes постепенно заменяет Pod'ы
	rollingUpdate: 
	   maxUnavailable: 25% # Максимум недоступных Pod'ов (или число) 
       maxSurge: 25% # Максимум дополнительных Pod'ов сверх replicas
	spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

Strategy:

```yaml
spec:
  strategy:
    type: Recreate # Сначала удаляет все Pod'ы, затем создаёт новые — приводит к простою
```

---
##### Процесс запуска Dashboard (такой же [[Компоненты - Kubernetes#Процесс запуска Pod-а|как и у обычного компонента]])

1. `kubectl` передаёт container-images + манифест Scheduler-у  - "надо его запустить"
2. **Control Manager** теперь знает, что должен быть запущен этот Pod (видит из **etsd**, что должен быть 1, а запущено 0), ищет Node, где можно запустить -> передаёт Scheduler-у, что надо запустить Pod.
3. **Scheduler** - планирует запуск Pod-а. Передаёт kubelet-у на нужной ноде, что нужно запустить Pod.
4. **Kubelet** - получает image (из локального docker registry), запускает Pod.
- **etcd** - постоянно мониторит состояние 

##### Этапы создания компонентов Deployment

1. Scheduled
2. Pull
3. Create
4. Start


---






----
#### [[Deployment - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

