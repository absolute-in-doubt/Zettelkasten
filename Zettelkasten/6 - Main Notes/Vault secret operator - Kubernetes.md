
14-03-2026 18:11

Status:

Tags: [[Kubernetes]]

---
# Vault secret operator - Kubernetes


**HashiCorp Vault** — централизованный менеджер секретов для динамического хранения, генерации и ротации секретов (пароли, API-ключи, сертификаты). В отличие от Kubernetes Secrets, Vault шифрует данные, ведёт аудит и поддерживает lifecycle секретов.

## Vault vs Kubernetes Secrets

|Аспект|Kubernetes Secrets|HashiCorp Vault|
|---|---|---|
|**Хранение**|etcd (base64, не шифрование)|Шифрованная база данных|
|**Доступ**|RBAC Kubernetes|Политики Vault + аудит|
|**Ротация**|Ручная|Автоматическая/динамическая|
|**Генерация**|Статические|Динамические (DB creds, certs)|
|**TTL**|Нет|Lease + auto-revoke|
|**Аудит**|Нет|Полный лог доступа|
|**Масштаб**|Только K8s|Multi-cloud + on-prem|

## Архитектура Vault в K8s

```
Vault Server (HA) ←→ Kubernetes Pods ←→ CSI Driver / Operator / Agent
     (encrypted)         (injection)         (mount/env)
```


---
## Популярные реализации интеграции

#### 1. **[[Vault Secrets Operator - Kubernetes|Vault Secrets Operator]]** (самый популярный 2026)

```yml
apiVersion: secrets.hashicorp.com/v1beta1
kind: VaultStaticSecret
metadata:
  name: db-creds
spec:
  vaultAuthRef: token-auth
  mount: kv/prod
  path: db/mysql
  destination:
    create: true
    name: mysql-secret
```

**Плюсы:** CRD, caching, RBAC-native.

**Установка:**
```shell
helm repo add hashicorp https://helm.releases.hashicorp.com helm install vault-secrets-operator hashicorp/vault-secrets-operator
```


#### 2. **CSI Secrets Store** (cloud-native)

```yml
# SecretProviderClass
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
spec:
  provider: vault
  parameters:
    vaultAddress: "https://vault:8200"
    secrets: |
      - path: kv/prod/db
```

**Плюсы:** tmpfs volumes, без K8s Secrets.

## 3. **External Secrets Operator** (GitOps-friendly)

```yml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
spec:
  secretStoreRef:
    name: vault-backend
  target:
    name: app-secrets
  dataFrom:
  - extract:
      key: kv/prod/app
```

**Плюсы:** Sync в K8s Secrets, Helm-чарты.


## Когда использовать что

```
Dev/Low-security:    K8s Secrets (stringData)
Prod/Single-cluster: External Secrets Operator
Enterprise/Multi-cloud: Vault Secrets Operator + CSI
Mission Critical:    Vault + dynamic secrets + HSM
```


----
#### [[Vault secret operator - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

