
14-03-2026 18:16

Status:

Tags:

---
# Vault Secrets Operator - Kubernetes

**HashiCorp Vault Secrets Operator (VSO)** — официальный оператор для синхронизации секретов из Vault в Kubernetes Secrets. Создаёт CRD `VaultStaticSecret`, который автоматически заполняет K8s Secret данными из Vault.

#### 1. Предварительные требования

##### Vault KV Secret (пример)

```shell
# В Vault создайте секрет
vault kv put kv-v2/myapp/config \
  db_password="supersecret" \
  api_key="abc123xyz"
```

##### Vault Auth Method (Kubernetes)

```shell
# Включите Kubernetes auth в Vault
vault auth enable kubernetes
vault write auth/kubernetes/config \
  kubernetes_host="https://kubernetes.default.svc"
```

---
#### 2. Установка Vault Secrets Operator

```shell
helm repo add hashicorp https://helm.releases.hashicorp.com
helm install vso hashicorp/vault-secrets-operator \
  --namespace vault-secrets-operator-system \
  --create-namespace \
  --version 0.13.0
```

---
#### 3. Настройка VaultAuthMethod (способ аутентификации)

```yml
apiVersion: secrets.hashicorp.com/v1beta1
kind: VaultAuthMethod
metadata:
  name: k8s-auth
  namespace: monitoring
spec:
  mount: kubernetes  # Vault mount path
  method: kubernetes
  mountTokenSecret: vault-token  # ServiceAccount JWT
  kubernetes:
    role: myapp-role
    serviceAccount: default
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: default
  namespace: monitoring
```


**Vault policy:**

```
path "kv-v2/data/myapp/*" {
  capabilities = ["read"]
}
vault write auth/kubernetes/role/myapp-role ...
```

~={red}**Запуск:**=~ `kubectl apply -f vault-auth.yaml -f secret.yaml`.


---
#### 4. VaultStaticSecret (создание K8s Secret из Vault)

##### Простой пример (статический секрет)

```yml
apiVersion: secrets.hashicorp.com/v1beta1
kind: VaultStaticSecret
metadata:
  name: myapp-config
  namespace: monitoring
spec:
  vaultAuthRef: k8s-auth  # Ссылка на VaultAuthMethod
  type: kv-v2            # Vault engine version
  mount: kv-v2           # Vault mount path
  path: myapp/config     # Путь к секрету
  
  destination:           # Создаваемый K8s Secret
    name: app-secrets
    create: true
    type: Opaque
  
  refreshAfter: 24h      # Обновление каждые 24ч
  rollbackOnFailure: true
```

**Результат:** K8s Secret `app-secrets` с ключами `db_password`, `api_key`.

##### С динамическими секретами (DB)

```yml
apiVersion: secrets.hashicorp.com/v1beta1
kind: VaultStaticSecret
metadata:
  name: db-dynamic
  namespace: monitoring
spec:
  vaultAuthRef: k8s-auth
  type: database
  mount: database
  path: mysql/creds/readonly
  
  destination:
    name: mysql-ro-creds
    create: true
  
  refreshAfter: 1h       # Ротация каждый час
```

##### С refresh по lease (автоматически)

```yml
spec:
  refreshByLease: true   # Следит за TTL Vault lease
```

---
#### 5. Использование в Deployment

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  namespace: monitoring
spec:
  template:
    spec:
      containers:
      - name: app
        image: myapp:latest
        env:
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: app-secrets      # Из VaultStaticSecret
              key: db_password
```

## 6. Полный пример манифеста

```yml
# vault-auth.yaml
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: vault-sa
  namespace: monitoring
---
apiVersion: secrets.hashicorp.com/v1beta1
kind: VaultAuthMethod
metadata:
  name: k8s-auth
  namespace: monitoring
spec:
  method: kubernetes
  mount: kubernetes
  kubernetes:
    role: monitoring-role
    serviceAccount: vault-sa
---
# secret.yaml
apiVersion: secrets.hashicorp.com/v1beta1
kind: VaultStaticSecret
metadata:
  name: monitoring-config
  namespace: monitoring
spec:
  vaultAuthRef: k8s-auth
  type: kv-v2
  mount: kv-v2
  path: monitoring/config
  
  destination:
    name: monitoring-secrets
    create: true
  
  refreshAfter: 12h
```

## 7. Проверка

```shell
kubectl get vaultstaticsecret -n monitoring
kubectl get secret monitoring-secrets -n monitoring -o yaml
kubectl get secret monitoring-secrets -o jsonpath='{.data.api_key}' | base64 -d
```

## Особенности

- **Автообновление**: Secrets синхронизируются по расписанию/lease
    
- **GitOps-ready**: CRDs можно хранить в Git
    
- **Rollback**: Восстанавливает предыдущую версию при ошибке
    
- **Dynamic secrets**: Поддержка DB creds, PKI certs
    




----
#### [[Vault Secrets Operator - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

