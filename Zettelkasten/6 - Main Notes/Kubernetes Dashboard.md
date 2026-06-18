
04-02-2026 17:43

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Kubernetes Dashboard

## Стартап (доступ к Dashboard)

1. Проверьте статус подов: `kubectl get pods -n kubernetes-dashboard` (убедитесь, что STATUS=Running).
    
2. Запустите прокси: `kubectl proxy` (для локального доступа на `http://localhost:8001`.
    
3. Получите токен: `kubectl -n kubernetes-dashboard create token admin-user`.
    
4. Откройте в браузере: [http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/).
    
5. Войдите с токеном (тип "Token").[](https://purpleschool.ru/knowledge-base/article/kubernetes-dashboard)
    

## Завершение сессии

- Остановите прокси: Ctrl+C в терминале с `kubectl proxy`.
    
- Ничего больше — Dashboard продолжает работать в фоне. Токен истекает автоматически (обычно 1 час), генерируйте новый при следующем доступе.​

----
#### [[Kubernetes Dashboard - Flashcards|Link to flashcards]]



---
### References:

