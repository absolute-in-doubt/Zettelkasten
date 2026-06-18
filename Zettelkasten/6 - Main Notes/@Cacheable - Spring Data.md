
15-02-2026 13:19

Status:

Tags: [[Spring Data JPA]]

---
# @Cacheable - Spring Data


**Назначение**: Кэширует результат метода по ключу (аргументам). При повторном вызове с тем же ключом возвращает значение из кэша, пропуская выполнение метода.  

**Параметры**: 
- `value` (имя кэша), 
- `key` (SpEL для ключа, по умолчанию все аргументы), 
- `condition` (условие кэширования), 
- `unless` (исключение).  

**Пример**:

```java
@Cacheable(value = "books", key = "#isbn")
public Book findBook(String isbn) {
    // Дорогой вызов БД
    return bookRepository.findByIsbn(isbn);
}
```

Вызов с тем же `isbn` вернёт из кэша.

### Связанные аннотации

- ~={purple}**@Cacheable**:=~ означает, что ~={red}**результат метода можно брать из кэша**=~, если для таких же параметров он уже есть

- ~={purple}**@CachePut(`value = "cacheName"`)**=~: **~={red}Всегда выполняет метод=~** и обновляет кэш (не пропускает). Для `updateBook(isbn, book)`.
    
- ~={purple}**@CacheEvict(`value = "cacheName"`)**=~: Удаляет записи из кэша (по ключу или все). Например можно поставить на методе удаления объекта.
    
- ~={purple}**@CacheConfig**=~: Общие настройки кэша на уровне класса (имя, manager).
    
- ~={purple}**@Caching**(`value = "users", key = "#id"`)=~: Комбинация нескольких операций:

```java
@Caching(
    cacheable = {
        @Cacheable(value = "users", key = "#id"),
        @Cacheable(value = "profiles", key = "#id")
    },
    evict = {
        @CacheEvict(value = "statistics", allEntries = true)
    }
)
public User findById(Long id) {
    ...
}
```
   

> [!tip] Note
> В качестве ключа можно указывать поле возвращаемого объекта. Это реализуется с помощью SpEL (Spring Expression Language):
> ```java
> @Cacheable(value = "users", key = "#id")           // параметр id
> @Cacheable(value = "users", key = "#user.id")      // поле id объекта user  
> @Cacheable(value = "users", key = "#id + '_' + #name") // комбинация параметров
> @Cacheable(value = "users")                        // ключ по умолчанию (все параметры)
> ```



---
## Настройка

Включите кэширование:

```java
@SpringBootApplication
@EnableCaching  // Обязательно!
public class App { ... }
```

Кэши: ConcurrentMapCache (память), Caffeine, **~={red}Redis=~**.

## Лучшие практики

- Используйте на сервисах, не в приватных методах (нужен прокси).
    
- Ключ: `#root.methodName + #id` для уникальности.
    
- Избегайте несогласованности: комбинируйте с `@CacheEvict` после обновлений

----
#### [[@Cacheable - Spring Data - Flashcards|Link to flashcards]]



---
### References:

