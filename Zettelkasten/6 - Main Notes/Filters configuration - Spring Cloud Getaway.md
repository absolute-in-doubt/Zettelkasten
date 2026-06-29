
19-06-2026 16:49

Status:

Tags: [[Spring Cloud Getaway]] [[Spring Cloud]] [[Spring]]

---
# Filters configuration - Spring Cloud Getaway



### StripPrefix

Очень часто используется.

Есть сервис:

```http
http://localhost:8081/api/users/123
```

Хотим обращаться через gateway так:

```http
GET /users/api/users/123
```

Конфиг:

```yaml
routes:
  - id: user-service
    uri: http://localhost:8081
    predicates:
      - Path=/users/**
    filters:
      - StripPrefix=1
```

Тогда Gateway уберёт первый сегмент пути:

```text
/users/api/users/123
↓
/api/users/123
```

и отправит на сервис.


---
### ~={red}RewritePath=~

Более гибкий вариант.

Например:

```yaml
routes:
  - id: user-service
    uri: lb://USER-SERVICE
    predicates:
      - Path=/users/**
    filters:
      - RewritePath=/users/(?<segment>.*), /${segment}
```

Получаем:

```text
/users/api/v1/profile
↓
/api/v1/profile
```

~={orange}На практике это один из самых популярных фильтров.=~


---
### AddRequestHeader

Добавить заголовок перед отправкой.

```yaml
filters:
  - AddRequestHeader=X-Gateway,true
```

Теперь каждый запрос к downstream сервису будет содержать:

```http
X-Gateway: true
```


---
### RemoveRequestHeader

```yaml
filters:
  - RemoveRequestHeader=Cookie
```

Удалит Cookie перед проксированием.


---
### SetPath

Полностью заменить путь.

```yaml
filters:
  - SetPath=/internal/users
```

Любой запрос будет отправлен на:

```http
/internal/users
```



----
#### [[Filters configuration - Spring Cloud Getaway - Flashcards|Link to flashcards]]



---
### References:

