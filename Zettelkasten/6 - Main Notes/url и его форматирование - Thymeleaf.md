
10-01-2026 10:56

Status:

Tags:

---
# url и его форматирование - Thymeleaf


### Базовый синтаксис `@{...}`

- Абсолютный URL:  
  `@{http://example.com/docs}`

- Контекстно‑относительный URL (к приложению):  
  `@{/users}`

- Относительный к корню сервера (другой контекст):  
  `@{~/billing/process}`

- Протокольно‑относительный:  
  `@{//cdn.example.com/lib.js}`

---
## Параметры в URL

- Параметры запроса (query string):
    
```html
<a th:href="@{/users(page=${page},size=${size})}">Next</a>
```
    
Результат: `/users?page=1&size=20` (с автоматическим экранированием).


- Путь с переменными (path variables):
    
```html
<a th:href="@{/users/{id}(id=${user.id})}">View</a>
```
    
Результат: `/users/15`


- Несколько параметров:
    
```html
<a th:href="@{/order/process(execId=${execId},mode='FAST')}">Process</a>
```

----
#### [[url и его форматирование - Thymeleaf - Flashcards|Link to flashcards]]



---
### References:

