
26-02-2026 19:38

Status:

Tags: [[Spring Data JPA]] [[Spring]]

---
# @PageableDefault - Spring Data



**`@PageableDefault`** задаёт **дефолтные значения** для `Pageable`, если параметры **НЕ переданы в HTTP query string**. 
~={purple}**Парсит НЕ из body**, а из **URL параметров**.=~

##### Откуда берутся значения (приоритет)

```
1. URL параметры (page, size, sort) — ✅ ВЫСШИЙ приоритет
2. @PageableDefault значения                    ↓
3. Глобальные дефолты Spring (page=0, size=20) ↓ НИЗШИЙ
```


----
### Параметры @PageableDefault

|Параметр|Тип|Дефолт|Описание|
|---|---|---|---|
|`value/size`|`int`|`10`|Размер страницы|
|`page`|`int`|`0`|Номер страницы (0-based)|
|`sort`|`String[]`|`{}`|Поля сортировки (`"name"`, `"age,desc"`)|
|`direction`|`Sort.Direction`|`ASC`|Направление сортировки|


---
## Примеры

#### 1. Полный пример

```java
@GetMapping("/users")
public Page<User> getUsers(
    @PageableDefault(size = 20, page = 0, 
                     sort = {"name", "age"}, 
                     direction = Sort.Direction.DESC)
    Pageable pageable
) {
    return userRepository.findAll(pageable);
}
```

#### 2. Разбор запросов

|Запрос|page|size|sort|direction|
|---|---|---|---|---|
|`/users`|`0`|`20`|`name DESC, age DESC`|`DESC`|
|`/users?page=2`|`2`|`20`|`name DESC, age DESC`|`DESC`|
|`/users?size=50`|`0`|`50`|`name DESC, age DESC`|`DESC`|
|`/users?page=1&size=10&sort=id,asc`|`1`|`10`|`id ASC`|`ASC`|

**Spring автоматически парсит**:

```
GET /users?page=1&size=10&sort=name,asc&sort=age,desc
↓
PageRequest.of(1, 10, Sort.by("name").ascending().and(Sort.by("age").descending()))
```

#### 3. Sort синтаксис в URL

```
sort=name                    → name ASC
sort=name,desc               → name DESC
sort=name,asc&sort=age,desc  → name ASC, age DESC
```

> [!example] Note
> **`sort` без дефолта** — случайный порядок


---
## Глобальная настройка (application.yml)

```yml
spring:
  data:
    web:
      pageable:
        default-page-size: 25
        max-page-size: 1000  # Максимум (защита от DoS)
        one-indexed-parameters: false  # page с 0 или 1?
        page-parameter: page
        size-parameter: size
```

**Теперь `@PageableDefault(size=20)` переопределит глобальный `default-page-size: 25`**.​


----
#### [[@PageableDefault - Spring Data - Flashcards|Link to flashcards]]



---
### References:

