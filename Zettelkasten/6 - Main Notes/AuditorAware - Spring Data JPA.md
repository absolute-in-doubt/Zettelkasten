
25-05-2026 13:29

Status:

Tags: [[Spring Data JPA]] [[Spring]]

---
# `AuditorAware<T>` - Spring Data JPA


**`AuditorAware<T>`** — это интерфейс из **Spring Data JPA**, который используется для **определения текущего аудитора** (пользователя или системы), выполняющего действия над сущностями. Он возвращает **идентификатор аудитора** (например, имя пользователя, ID или строку типа `"system"`), который автоматически подставляется в поля сущностей, помеченные аннотациями `@CreatedBy` и `@LastModifiedBy`.

---

## Зачем нужен аудитор?

Аудитор позволяет **отслеживать, кто создал или изменил запись** в базе данных. Это полезно для:

- **Логгирования изменений** (кто и когда изменил данные).
- **Аудита безопасности** (например, для отчётности или расследований).
- **Отладки** (понимание, какие действия выполнял пользователь).

**Пример:** Если пользователь с именем `"admin"` создаёт или обновляет сущность, то в полях `@CreatedBy` и `@LastModifiedBy` будет записано `"admin"`.

---

## Как работает `AuditorAware<T>`?

### Базовый интерфейс

- Метод `getCurrentAuditor()` должен вернуть **`Optional<T>`** с идентификатором текущего аудитора.
- Если аудитор не определен, возвращается `Optional.empty()`.


---

## Как подключается к сущностям?

Чтобы аудитор работал, нужно:

1. **Включить аудирование** (`@EnableJpaAuditing`).
2. **Зарегистрировать бин `AuditorAware<T>`** (как в примере выше).
3. **Пометить поля сущности** аннотациями `@CreatedBy` и `@LastModifiedBy`.

**Пример сущности:**

---

### Какие бывают реализации `AuditorAware<T>`?

#### 1. Простая реализация (фиксированное значение)

- **Для чего:** Подходит для тестов или системных операций, где аудитор всегда один и тот же.

```java
@Bean
public AuditorAware<String> auditorProvider() {
    return () -> Optional.of("system");
}
```


---

#### 2. Аудитор на основе текущего пользователя (Spring Security)

Если в проекте используется **Spring Security**, можно получить имя текущего пользователя из контекста безопасности:

- **Для чего:** Автоматически подставляет имя пользователя, выполнившего действие.

```java
@Bean
public AuditorAware<String> auditorProvider() {
    return () -> Optional
    .ofNullable(SecurityContextHolder.getContext().getAuthentication())
    .map(authentication -> authentication.getName());
}
```

---

#### Аудитор на основе JWT или токена

Если пользователь определяется по токену (например, в REST API):

```java
@Bean
public AuditorAware<String> auditorProvider() {
    return () -> {
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
        if (authentication != null && authentication.getPrincipal() instanceof UserDetails) {
            return Optional.of(((UserDetails) authentication.getPrincipal()).getUsername());
        }
        return Optional.of("anonymous");
    };
}
```


---

#### Кастомная логика (например, из контекста запроса)

Можно реализовать свою логику, например, брать ID пользователя из заголовков запроса или из потока (ThreadLocal):

- **Для чего:** Подходит для сложных систем, где аудитор определяется нестандартным способом.

```java
@Bean
public AuditorAware<Long> auditorProvider() {
    return () -> Optional.ofNullable(UserContextHolder.getCurrentUserId());
}
```

---

#### Аудитор на основе UUID или случайного значения

- **Для чего:** Может использоваться для генерации уникальных идентификаторов сессий.

```java
@Bean
public AuditorAware<String> auditorProvider() {
    return () -> Optional.of(UUID.randomUUID().toString());
}
```


---

## **Какие аннотации работают с аудитором?**

| Аннотация           | Описание                                      | Пример                                                      |
| ------------------- | --------------------------------------------- | ----------------------------------------------------------- |
| `@CreatedBy`        | Заполняется **при создании** сущности         | `@CreatedBy private String createdBy;`                      |
| `@LastModifiedBy`   | Обновляется **при каждом изменении** сущности | `@LastModifiedBy private String lastModifiedBy;`            |
| `@CreatedDate`      | Дата создания (работает без аудитора)         | `@CreatedDate private LocalDateTime createdDate;`           |
| `@LastModifiedDate` | Дата изменения (работает без аудитора)        | `@LastModifiedDate private LocalDateTime lastModifiedDate;` |

---

### Уровень в Clean Architecture

В **Clean Architecture** аудитор и связанные с ним компоненты относятся к **уровню инфраструктуры**, а точнее — к **слою персистентности (infrastructure.persistence)**.



----
#### [[AuditorAware - Spring Data JPA - Flashcards|Link to flashcards]]



---
### References:

