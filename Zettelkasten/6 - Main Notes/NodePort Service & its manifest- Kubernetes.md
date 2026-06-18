
04-02-2026 16:52

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# NodePort Service - Kubernetes

Not recommended to use. Nodes come and go. You don't want to make a node public. + ports resstrictions on available ports for NodePort.


![[Pasted image 20260204165034.png]]

Kube-Proxy - проксирует запросы пользователя.

NodePort связывает Kube-Proxy с Pod-ом.


![[Pasted image 20260204165446.png]]

```yaml
apiVersion: v1
kind: Service
metadata:
	 name: postgres-node-ports
spec:
	 type: NodePort
	ports:
		- port: 80 # порт внутри кластера, по которому другие Pod-ы могут достучаться к этому Pod-у через данный сервис
		targetPort: 5432 # порт контейнера
		nodePort: 31000 # Внешний порт - Pod будет доступен извне по localhost:31000, использует порты нашего ПК (30000 - 32676)
	selector:
		app: postgres # label того Pod, к которому мы подключаемся
```
> [!tip]
> В качестве внутреннего внутриклатерного пора лучше использовать `port: 80` - везде одинаковый (http default).
> 
> Если запускаем контейнеры (в Pod-ах) с unprivileged user (для безопасности) - не можем использовать порты меньше 1024. Service решает эту проблму, мапя порты - порт контейнера остаётся любым, но сам Service делает его доступным внутри кластера на порте 80.

Часть декларации сервиса, к которому подключаемся:

```yaml
apiVersion: v1 
kind: Pod 
metadata:
	name: postgres
	labels: 
		app: postgres  # это произвольная K-V пара, так же указываем и на сервисе
```

----
#### [[NodePort Service - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

