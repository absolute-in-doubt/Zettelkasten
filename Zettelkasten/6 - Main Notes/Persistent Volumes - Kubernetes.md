
05-02-2026 14:18

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Persistent Volumes - Kubernetes


Чтобы подключить Persistent Volumes (PV) к контейнерам в Kubernetes Deployment, нужно последовательно создать StorageClass (опционально), PersistentVolume (PV), PersistentVolumeClaim (PVC) и указать PVC в спецификации Deployment.

## Шаги подготовки

Сначала создайте StorageClass для динамического provisioning (если не используете статический PV):

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: my-storage
provisioner: kubernetes.io/no-provisioner  # Или ваш провайдер, напр. nfs.csi.k8s.io
```

Затем PVC, который запрашивает хранилище:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes: [ReadWriteOnce]
  resources:
    requests:
      storage: 10Gi
  storageClassName: my-storage
```

Примените: `kubectl apply -f pvc.yaml`.

## Deployment с PVC

В Deployment укажите volumes и volumeMounts в spec.template.spec:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres-deployment
  labels:
    app: postgres
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:15-bookworm
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_DB
          value: mydatabase
        - name: POSTGRES_USER
          value: myuser
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: password
        volumeMounts:
        - mountPath: /var/lib/postgresql/data
          name: postgres-storage
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
      volumes:
      - name: postgres-storage
        persistentVolumeClaim:
          claimName: postgres-pvc
```

Примените: `kubectl apply -f deployment.yaml`. Данные в /data сохранятся при перезапуске подов.

## Проверка и особенности

Проверьте статус: `kubectl get pvc` и `kubectl describe pod <pod-name>`. PV монтируется автоматически при запуске подов Deployment. Для ReadWriteMany (NFS) используйте соответствующий accessMode; локальные PV (ReadWriteOnce) привязаны к ноде.


----
#### [[Persistent Volumes - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

