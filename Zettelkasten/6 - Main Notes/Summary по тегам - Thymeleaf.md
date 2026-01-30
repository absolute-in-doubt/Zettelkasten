
06-01-2026 13:12

Status:

Tags: [[Thymeleaf - шаблонизатор]] [[Spring]]

---
# Summary по тегам - Thymeleaf


## Основные атрибуты  Thymeleaf
	
- **Фрагменты и layout**:
    
    - `th:fragment`, `th:insert`, `th:replace` — переиспользуемые куски разметки.
        
- **Формы**:
    
    - `th:action`, `th:object`, `th:field`, `th:errors`, `th:errorclass` и др.​
        

Все они используют стандартные выражения Thymeleaf (см. далее).​

## Выражения и обращение к Model

Основные типы выражений (в шаблонах Spring MVC — через Spring EL):​

- `${...}` — **variable expression**: обращение к атрибутам Model (`model.addAttribute("user", user)` → `${user}`):
    
    - Свойства: `${user.name}`, `${user.address.city}`.
        
    - Методы: `${user.fullName()}`, `${user.admin ?: 'guest'}`.
        
- `*{...}` — **selection expression**: как `${...}`, но относительно текущего выбранного объекта (`th:object`).​
    
- `#{...}` — сообщения i18n (`messages.properties`).
    
- `@{...}` — ссылки/URL: `@{/users/{id}(id=${user.id})}`.​
    
- `~{...}` — фрагменты: `~{fragments/header :: main}`.​
    

Примеры обращения к полям и методам из Model:​


```xml
<p th:text="${user.name}">Имя</p> 
<p th:text="${user.getAge()}">Возраст</p> 
<p th:text="${user.address?.city}">Город</p> 
<li th:each="u : ${users}" th:text="${u.email}">email</li>
```


Если задан `th:object="${user}"`, внутри можно писать `*{name}` вместо `${user.name}`.​


---
## Использование Model в формах: th:object и th:field

Правильная работа с формами в Thymeleaf строится на связке **`th:object` + `th:field`**.​

## Привязка формы к объекту

```xml
<form th:action="@{/users}" th:object="${user}" method="post">    
	<input type="text" th:field="*{name}" />    
	<input type="email" th:field="*{email}" />    
	<input type="password" th:field="*{password}" />    
<button type="submit">Сохранить</button> </form>```

- `th:object="${user}"` говорит: форма работает с объектом Model под именем `user`.​
    
- `th:field="*{name}"` означает: поле связано с `user.name`. `th:field` **сам** проставит правильные `name`, `id` и `value`, что важно для биндинга и валидации.​


## Ошибки и валидация

При использовании Spring Validation можно выводить ошибки:

```xml
<div>     
	<input type="text" th:field="*{name}" />    
	<span th:if="${#fields.hasErrors('name')}"          th:errors="*{name}">Ошибка</span> 
</div>```

- `th:errors="*{name}"` выведет текст ошибки для поля `name`.
    
- `#fields.hasErrors('name')` использует встроенный объект `#fields`.​
    

## Рекомендации по работе с Model в формах

Чтобы формы работали корректно и чисто:​

- Использовать **один объект** на форму: `th:object="${userForm}"`, а внутри — `th:field="*{prop}"`.
    
- Не писать вручную `name="user.name"`/`value="${user.name}"`, если можно использовать `th:field` — оно лучше поддерживает валидацию и повторный вывод формы.​
    
- Для коллекций (списки элементов с редактированием) удобно использовать индексы: `th:each="item, stat : *{items}"` и `th:field="*{items[__${stat.index}__].name}"`.​
    
- Для label + input связок использовать `th:for="*{field}"` на `<label>` и `th:field="*{field}"` на `<input>` — Thymeleaf автоматически синхронизирует `for` и `id`.​

```xml
<form th:object="${user}">
    <div>
        <label th:for="*{email}">Email</label>
        <input type="email" th:field="*{email}" />
    </div>
</form>
```

Такой подход даёт корректный пост‑бэкенд биндинг (`@ModelAttribute` / аргумент контроллера), повторный показ введённых значений при ошибках, и удобный доступ к сообщениям валидации.

---
### References:

