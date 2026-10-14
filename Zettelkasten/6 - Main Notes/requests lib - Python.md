
Index: [[Python]]




Установка:

```bash
pip install requests
```

GET:

```python
import requests

response = requests.get("https://api.example.com/users")

print(response.status_code)
print(response.text)
```

POST с JSON:

```python
response = requests.post(
    "https://api.example.com/users",
    json={"name": "John", "age": 25}
)
```

`requests` автоматически сериализует `dict` в JSON.

---

### 2. Headers и query parameters

Query parameters:

```python
response = requests.get(
    "https://api.example.com/users",
    params={"page": 1, "limit": 20}
)
```

Получится примерно:

```text
/users?page=1&limit=20
```

Headers:

```python
response = requests.get(
    url,
    headers={
        "Authorization": "Bearer TOKEN",
        "Accept": "application/json"
    }
)
```

---

### 3. Работа с JSON

Получить JSON:

```python
data = response.json()
```

Например:

```python
response = requests.get(url)

if response.status_code == 200:
    users = response.json()
```

Отправить JSON:

```python
requests.post(
    url,
    json={"name": "John"}
)
```

---

### 4. Проверка HTTP-ошибок

Важно: `requests.get()` **не выбрасывает exception автоматически** при `4xx`/`5xx`.

Можно сделать:

```python
response = requests.get(url)
response.raise_for_status()
```

Тогда для HTTP-ошибок будет выброшено `requests.HTTPError`.

Например:

```python
try:
    response = requests.get(url, timeout=5)
    response.raise_for_status()
except requests.Timeout:
    print("Timeout")
except requests.HTTPError as e:
    print("HTTP error:", e)
except requests.RequestException as e:
    print("Request error:", e)
```

`RequestException` — базовое исключение для ошибок `requests`.

---

### 5. `timeout` — обязательно для production-кода

Не стоит делать:

```python
requests.get(url)
```

без timeout.

Лучше:

```python
requests.get(url, timeout=5)
```

Иначе запрос потенциально может зависнуть на неопределённое время.

---

### 6. Другие HTTP-методы

```python
requests.get(url)
requests.post(url, json=data)
requests.put(url, json=data)
requests.patch(url, json=data)
requests.delete(url)
```

---

### 7. `requests.Session`

Если делаешь много запросов к одному API, можно использовать `Session`:

```python
session = requests.Session()

session.headers.update({
    "Authorization": "Bearer TOKEN"
})

response = session.get(url)
response = session.post(url, json=data)
```

Session позволяет переиспользовать TCP-соединения и общие настройки.

---

### 8. Async HTTP

`requests` — синхронная библиотека.

Для async Python часто используют `httpx`:

```python
import httpx

async with httpx.AsyncClient() as client:
    response = await client.get(url)
```

Это особенно актуально для приложений на `asyncio`, FastAPI и т.п.

---

### Что запомнить

```text
requests.get()       → GET
requests.post()      → POST
params={}            → query parameters
json={}              → JSON body
headers={}           → HTTP headers
response.json()      → JSON → Python object
response.text        → response body как string
response.status_code → HTTP status
raise_for_status()   → 4xx/5xx → exception
timeout=5            → ограничение времени ожидания
Session              → повторное использование соединений
```