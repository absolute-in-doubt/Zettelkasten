
03-03-2026 13:38

Status:

Tags: [[CORS]] [[System Design]] [[Spring Web MVC]] [[Spring Security]]

---
# CORS overview

CORS (Cross-Origin Resource Sharing) — это механизм безопасности браузеров, позволяющий серверу указывать, какие внешние домены могут получать доступ к его ресурсам, обходя ограничения Same-Origin Policy (SOP).

## Основная идея

![[Pasted image 20260303134403.png]]

Браузеры по умолчанию блокируют запросы (например, через XMLHttpRequest или fetch) с одного домена к ресурсам другого (разный протокол, домен или порт). CORS решает это через специальные HTTP-заголовки: сервер отвечает на "предварительный запрос" (preflight с методом OPTIONS), указывая разрешённые origins, методы (GET, POST и т.д.), заголовки и credentials.

## Типы запросов

![[Pasted image 20260303134601.png]]

- **Простые**: GET, HEAD, POST с базовыми заголовками — сервер сразу добавляет Access-Control-Allow-Origin.


![[Pasted image 20260303134625.png]]

- **Сложные (preflight)**: Для DELETE, PUT или с кастомными заголовками — браузер сначала шлёт OPTIONS-запрос для проверки.

> [!note] 
> Сам Main запрос в preflight CORS request exchange НЕ ДОЛЖЕН содержать `Access-Control-Request-*`. 
> Они должны быть только в OPTIONS запросе. 

#### Заголовки preFlight запроса (OPTIONS)

```
Access-Control-Request-Method: POST
Access-Control-Request-Headers: content-type,x-pingother
```

- `Access-Control-Request-Method` - сообщает о том, что Main запрос будет иметь тип POST.
	
- `Access-Control-Request-Headers` - сообщает, что Main запрос будет содержать кастомные заголовки `content-type` и `x-pingother`. 


#### Заголовки preFlight ответа

```
Access-Control-Allow-Origin: https://foo.example
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
Access-Control-Max-Age: 86400
```

- `Access-Control-Allow-Origin` - разрешает доступ только этому сайту (`https://foo.example`)
	
- `Access-Control-Allow-Methods` - разрешает доступ с POST, GET и OPTIONS
	
- `Access-Control-Allow-Headers` - подтверждение списка валидных принимаемых заголовков
	
- `Access-Control-Max-Age` - время в секундах, сколько можно держать в кэше результат этого запроса. Т.е. тут 86400 сек = 24 часа. -> результат запроса браузер может закэшировать на 24 часа и отпрвалять повторный запрос только, когда кэш будет инвалидирован.

----
#### [[CORS overview - Flashcards|Link to flashcards]]



---
### References:

