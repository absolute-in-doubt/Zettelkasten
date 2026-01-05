
05-01-2026 10:35

Status:

Tags: [[Jakarta.servlet]] [[Java+]]

---
# JSP & JSTL

JavaServer Pages (JSP) — серверная технология Jakarta EE для создания динамического web‑контента путём встраивания Java‑выражений и тегов в HTML‑страницы.

~={cyan}JSP транслируется контейнером в сервлет и выполняется как Java‑код=~, что позволяет отделить оформление (HTML) от логики контроллера и уменьшить объём Java‑кода в представлении.



JSTL (JavaServer Pages Standard Tag Library) — стандартная библиотека тегов для JSP, содержащая готовые теги для условий, циклов, форматирования, i18n, работы с XML и др.


```xml
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Список пользователей</title>
</head>
<body>
<h1>Список пользователей</h1>

<%-- 
    Ожидается, что сервлет положил в request атрибут:
    request.setAttribute("users", List<User>);
    где User имеет поля: id, name, email
--%>

<c:if test="${empty users}">
    <p>Пользователи не найдены.</p>
</c:if>

<c:if test="${not empty users}">
    <table border="1" cellpadding="5" cellspacing="0">
        <thead>
        <tr>
            <th>ID</th>
            <th>Имя</th>
            <th>E-mail</th>
        </tr>
        </thead>
        <tbody>
        <c:forEach var="user" items="${users}">
            <tr>
                <td>${user.id}</td>
                <td>${user.name}</td>
                <td>${user.email}</td>
            </tr>
        </c:forEach>
        </tbody>
    </table>
</c:if>

</body>
</html>

```

----
#### [[JSP & JSTL - Flashcards|Link to flashcards]]



---
### References:

