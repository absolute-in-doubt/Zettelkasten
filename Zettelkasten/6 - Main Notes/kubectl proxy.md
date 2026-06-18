
14-03-2026 12:13

Status:

Tags:

---
# kubectl proxy


**`kubectl proxy`** запускает локальный HTTP-прокси-сервер (по умолчанию порт 8001), который перенаправляет запросы к Kubernetes API без необходимости аутентификации.

## Основное назначение

- **Цель**: Прокси к **~={red}Kubernetes API=~ серверу**
    
- **Доступ**: `http://localhost:8001/api/v1/pods`
    
- **Протокол**: Только **HTTP**
    
- **Применение**: Просмотр ~={red}ресурсов кластера=~ через REST API

## Примеры использования

```bash
# Базовый запуск (порт 8001)
kubectl proxy

# Другой порт
kubectl proxy --port=8080

# Проверка API версий
curl http://localhost:8001/api

# Список подов в monitoring
curl http://localhost:8001/api/v1/namespaces/monitoring/pods

# Остановить Ctrl+C
```

|Характеристика|kubectl proxy|kubectl port-forward|
|---|---|---|
|**Точка назначения**|API сервер|Под/Service|
|**Протокол**|HTTP|TCP/UDP|
|**Назначение**|API запросы|Приложение в поде|
|**Порт назначения**|6443 (API)|Любые (80, 3000, 9090)|
|**Пример**|`curl /api/pods`|`localhost:3000 → Grafana`|

**Преимущества**: Работает с текущим kubeconfig, не требует порт-форвардинга, идеально для быстрой диагностики API.


---
### References:

