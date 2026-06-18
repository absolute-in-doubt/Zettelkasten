
05-02-2026 10:13

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# LoadBalancer Service & its manifest - Kubernetes

> [!warning]
> В Kubernetes KinD LoadBalancer-сервисы не работают по умолчанию без дополнительных инструментов, но их можно реализовать с помощью cloud-provider-kind или сторонних решений вроде MetalLB.
> 
> Необходимо на хосте установить `cloud-provider-kind` - обработчик LoadBalancer-запросов.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: foo-app
  labels:
    app: http-echo
spec:
  containers:
  - name: foo-app
    image: registry.k8s.io/e2e-test-images/agnhost:2.39
    command:
    - /agnhost
    - serve-hostname
    - --http=true
    - --port=8080
---
apiVersion: v1
kind: Pod
metadata:
  name: bar-app
  labels:
    app: http-echo
spec:
  containers:
  - name: bar-app
    image: registry.k8s.io/e2e-test-images/agnhost:2.39
    command:
    - /agnhost
    - serve-hostname
    - --http=true
    - --port=8080
---
apiVersion: v1
kind: Service
metadata:
  name: foo-service
spec:
  type: LoadBalancer
  selector:
    app: http-echo
  ports:
  - port: 80 # внешний порт (вне кластера)
    targetPort: 8080 # порт контейнера (Pod-а)

```

> [!example] Note
> В LoadBalancer-сервисе Kubernetes **внутренний порт не нужно отдельно устанавливать** — он автоматически совпадает с внешним портом (`ports.port`), который используется и для CLUSTER-IP, и для EXTERNAL-IP.

```
external traffic -> LщadBalancer:80 -> http-echo-Pods:8080
```

> [!tip]
> В качестве внутреннего внутрикластерного порта лучше использовать `port: 80` - везде одинаковый (http default).
> 
> Если запускаем контейнеры (в Pod-ах) с unprivileged user (для безопасности) - не можем использовать порты меньше 1024. Service решает эту проблму, мапя порты - порт контейнера остаётся любым, но сам Service делает его доступным внутри кластера на порте 80.

---
#### Плюсы

- default way of exposing a service


#### Минусы

- doesn't prrovide **filtering, routing**
- нет фильтрации по типам запросов (HTTP, TCP, UDP, ...)
- Придётся создать по LoadBalancer под каждый сервис, that we want to expose:
![[Pasted image 20260209082018.png]]


---
### Способы балансировки

![[Pasted image 20260220095701.png]]

----
#### [[LoadBalancer Service & its manifest - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

