
10-01-2026 11:32

Status:

Tags: [[Thymeleaf - шаблонизатор]] [[Spring]]

---
# button - Thymeleaf

Всё то же самое, как и [[button - HTML|в HTML]].

Но для динамически изменяемого url используем свойство `th:onclick`

- `th:onclick` - указывает действие при нажатии кнопки. ~={orange}Отличается от обычного тем, что его содержимое может редактировать Thymeleaf.=~

Так сработает:
```html
<button type="button"
        th:onclick="|location.href='/users/${user.id}'|">
    Edit user
</button>

<!-- Или через url форматирование Thymeleaf: -->
<button type="button"
        th:onclick="|location.href='@{/users/{id}(id=${user.id})}'|">
    Edit user
</button>
```

А так нет - т.к. Thymeleaf не может редактировать обычные атрибуты:
```html
<button type="button" onclick="location.href='/users/${user.id}'">
	Edit user
</button>
```



----
#### [[button - Thymeleaf - Flashcards|Link to flashcards]]



---
### References:

