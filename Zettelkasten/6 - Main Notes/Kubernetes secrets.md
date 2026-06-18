
14-03-2026 18:04

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Kubernetes secrets


Kubernetes Secrets — это API-объекты для хранения чувствительных данных (пароли, токены, ключи) в base64-кодированном виде. Они монтируются в поды как переменные окружения или файлы.

## Типы Secrets

- **Opaque** — универсальный тип для произвольных данных
    
- `kubernetes.io/dockerconfigjson` — для Docker registry
    
- `kubernetes.io/tls` — TLS-сертификаты
    
- `bootstrap.kubernetes.io/token` — для bootstrap-токенов
   

---
## Способы создания

#### 1. Через kubectl (самый простой)

```shell
kubectl create secret generic db-pass \
  --from-literal=password=mypassword123 \
  --from-literal=username=admin \
  -n monitoring
```

#### 2. YAML с `data` (base64 вручную)

```yml
apiVersion: v1
kind: Secret
metadata:
  name: db-pass
  namespace: monitoring
type: Opaque
data:
  username: YWRtaW4=      # echo -n 'admin' | base64
  password: bXlwYXNzd29yZDEyMw==  # echo -n 'mypassword123' | base64
```


#### 3. YAML с `stringData` (удобнее)


```yml
apiVersion: v1
kind: Secret
metadata:
  name: db-pass
  namespace: monitoring
type: Opaque
stringData:
  username: admin           # Kubernetes закодирует автоматически
  password: mypassword123
# data: {}  # auto-generated
```


---
## Использование в Pod/Deployment

#### 1. Переменные окружения

```yml
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      containers:
      - name: app
        image: myapp
        env:
        - name: DB_USER
          valueFrom:
            secretKeyRef:
              name: db-pass
              key: username
        - name: DB_PASS
          valueFrom:
            secretKeyRef:
              name: db-pass
              key: password
```


#### 2. Файлы (volume)


```yml
spec:
  containers:
  - name: app
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/secrets
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: db-pass
```

**В контейнере:** `/etc/secrets/username`, `/etc/secrets/password`


## 3. Все ключи сразу (envFrom)

```yml
spec:
  containers:
  - name: app
    envFrom:
    - secretRef:
        name: db-pass
```

**Автоматически:** `DB_USER`, `DB_PASS` (имена в uppercase)


---
## Полезные команды


```shell
# Просмотр (НЕ декодирует!)
kubectl get secret db-pass -n monitoring -o yaml

# Декодировать
kubectl get secret db-pass -n monitoring -o jsonpath='{.data.password}' | base64 -d

# Редактировать
kubectl edit secret db-pass -n monitoring

# Удалить
kubectl delete secret db-pass -n monitoring
```


---
## Безопасность

- **Base64 ≠ шифрование** (легко декодируется)
    
- Secrets хранятся в etcd **в plaintext**
    
- Используйте **RBAC** для ограничения доступа
    
- Для продакшена: **External Secrets Operator**, **Vault**, **Sealed Secrets**
    

**Лучшая практика:** `kubectl create secret` + `stringData` для dev, External Secrets для продакшена.


----
#### [[Kubernetes secrets - Flashcards|Link to flashcards]]



---
### References:

