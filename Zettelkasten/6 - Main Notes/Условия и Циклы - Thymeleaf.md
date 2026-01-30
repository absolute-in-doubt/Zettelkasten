
20-10-2025 08:44

Status: #not_even_born 

Tags: [[Thymeleaf - шаблонизатор]] [[Spring]]

---
# Циклы


Цикл + применение метода

```html
<div th:each="person : ${peopleList}">  
    <a th:href="@{/people/{id}(id=${person.getId()})}" th:text="${person.getName()}">user</a>  
    <!--user будет заменено на имя пользователя -->
</div>
```

`th:href="@{/people/{id}(id=${person.getId()})}"` - делаем эту ссылку кликабедьной с [[url и его форматирование - Thymeleaf|динамически изменяемым url]]: 
	в url `/people/{id}` будет подставлен результат `person.getId()`


---
## Условия

    
    - `th:if`, `th:unless` — условный вывод.

```html
<ul>
    <!-- показывать только взрослых активных пользователей с именем, начинающимся на A -->
    <li th:each="user : ${users}"
        th:if="${user.active and user.age >= 18 and #strings.startsWith(user.name, 'A')}">
        <span th:text="${user.name}">Имя</span>
        <span th:text="${user.age}">Возраст</span>
    </li>
</ul>
```


----
#### [[Циклы - Thymeleaf - Flashcards|Link to flashcards]]



---
### References:

