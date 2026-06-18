
04-02-2026 15:40

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Манифест развёртывания Pod-a - Kubernetes

Есть два подхода:
- **Императивный** - одноразово командой:
```bash
kubectl run nginx --image=nginx --restart=Never
```

- **Декларативный** - настройка развёртывания через `.yaml`. МОжет использоваться многоразово, версионируется, хранится вметсе с кодом в git repository:
```yml
apiVersion: v1
kind: Pod
metadata:
	name: nginx
	labels:
		env: test
spec:
	containers:
	- name: nginx]
	  image: nginx
	  imagePullPolicy: IfNotPresent
	nodeSelector:
		diskType: ssd
```


### Подробно про декларативную конфигурацию

```yml
apiVersion: v1 # версия Kubernetes API 
kind: Pod  # тип создаваемого объекта
metadata:
	name: postgres
	labels: # children of labels могут быть совершенно любые k-v праы
		app: postgres
spec:   # зависит от типа создаваемого объекта
	containers:
	- name: postgres
	  image: postgres:15.15-bookworm
	  ports: #открывает порты для доступа
		- containerPort: 5432
	  env:
		- name: POSTGRES_USER
		value: postgres
		- name: POSTGRES_PASSWORD
		value: postgres
	resources:
		limits:
			memory: "128Mi" # контейнер может использовать не более 128 Mb
			cpu: "500m" # не более половины процессора (1000m - 1 ядро)
		requests:
			memory: "64Mi"
			cpu: "250m"
```

После применения конфигурации, её части будут храниться в [[Компоненты - Kubernetes#Node|etcd]]. (Информация о том, что сейчас должно быть запущено)


##### Версии Kubernetes API

![[Pasted image 20260204155143.png]]

#### Применение этой декларации для развёртывания

```bash
kubectl apply -f <.yaml_config_file_name>
```

----
#### [[Развёртывание Pod-а - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

