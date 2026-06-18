
25-05-2026 13:20

Status:

Tags: [[Spring Data JPA]] [[Spring]]

---
# Entity Auditing - Spring Data JPA


Вот подробный разбор аннотаций **@EnableJpaAuditing**, **@EntityListeners**, **@CreatedDate**, **@LastModifiedDate** в **Spring Data JPA**, а также их поведение при работе с **MapStruct**.

---

## ~={purple}@EnableJpaAuditing=~

**Назначение:** Включает **автоматическое аудирование (auditing)** сущностей в Spring Data JPA. Позволяет автоматически заполнять поля типа `createdDate`, `lastModifiedDate`, `createdBy`, `lastModifiedBy` при сохранении или обновлении сущности.

**Где используется:**

- На классе конфигурации Spring (обычно  в классе с `@Configuration`).

**Пример:**

```java
@Configuration
@EnableJpaAuditing
public class JpaConfig {
    // Дополнительная настройка аудитора (опционально)
    @Bean
    public AuditorAware<String> auditorProvider() {
        return () -> Optional.of("system");
    }
}
```

**Что делает:**

- Активирует механизм аудирования для всех сущностей, помеченных `@EntityListeners(AuditingEntityListener.class)`.
- По умолчанию использует `AuditingEntityListener` для обработки событий сущностей.

---

## ~={purple}@EntityListeners=~

**Назначение:** Указывает, какие **слушатели (listeners)** должны обрабатывать события жизненного цикла сущности (например, `@PrePersist`, `@PreUpdate`).

**Где используется:**

- На классе сущности (`@Entity`).

**Пример:**

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class User {
    // Поля сущности
}
```

**Что делает:**

- Подключает `AuditingEntityListener` к сущности, чтобы он мог автоматически заполнять поля аудирования (например, `@CreatedDate`, `@LastModifiedDate`).
- Можно указывать несколько слушателей, если нужно кастомное поведение.

---

## ~={purple}@CreatedDate=~ и ~={purple}@LastModifiedDate=~

**Назначение:** Автоматически заполняют поля сущности **датами создания и изменения** при сохранении или обновлении.

**Где используется:**

- На полях сущности (обычно `LocalDateTime`, `Instant`, `Date`).

**Пример:**
```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @CreatedDate
    @Column(updatable = false, nullable = false)
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime lastModifiedDate;

    // Геттеры и сеттеры
}
```

**Что делает:**

- `@CreatedDate`: Заполняется **один раз** при первом сохранении сущности (`@PrePersist`).
- `@LastModifiedDate`: Обновляется **при каждом изменении** сущности (`@PreUpdate`, `@PrePersist`).

**Важно:**

- Поле с `@CreatedDate` обычно помечают как `updatable = false`, чтобы оно не изменялось при обновлении.
- Для работы требуется **@EnableJpaAuditing** и **@EntityListeners(AuditingEntityListener.class)**.

---

### Как это работает вместе?

1. ~={purple}**@EnableJpaAuditing**=~ включает механизм аудирования на уровне приложения.
2. ~={purple}**@EntityListeners(`AuditingEntityListener.class`)**=~ подключает слушатель к конкретной сущности.
3. ~={purple}**@CreatedDate**=~ и ~={purple}**@LastModifiedDate**=~ автоматически заполняются слушателем при сохранении/обновлении.

---

### Поведение с MapStruct**


### Маппинг полей аудирования

- Если в DTO есть поля, соответствующие `createdDate`/`lastModifiedDate`, их нужно **явно маппировать** или игнорировать.
- **Пример маппера:**

```java
@Mapper(componentModel = "spring")
public interface UserMapper {
    @Mapping(target = "createdDate", ignore = true) // Игнорируем, так как заполняется автоматически
    @Mapping(target = "lastModifiedDate", ignore = true)
    UserDto toDto(User user);

    @Mapping(target = "createdDate", ignore = true)
    @Mapping(target = "lastModifiedDate", ignore = true)
    User toEntity(UserDto userDto);
}
```

   - **Почему игнорируем?** Потому что эти поля заполняются **только на уровне JPA**, а не через маппер.


### Создание новых сущностей через MapStruct

- Если вы создаёте **новую сущность** через маппер (например, `User user = userMapper.toEntity(userDto);`), то поля `@CreatedDate` и `@LastModifiedDate` **не будут заполнены автоматически**.
- Они заполнятся **только после вызова `repository.save(user)`**, так как аудирование срабатывает на уровне JPA.

### Обновление сущностей через MapStruct

- Если вы обновляете сущность через маппер (например, `userMapper.updateEntityFromDto(userDto, user);`), то поле `@LastModifiedDate` **обновится автоматически** при следующем `save()`.

### Кастомные мапперы для аудирования

- Если нужно, чтобы маппер **самостоятельно заполнял** `createdDate`/`lastModifiedDate` (например, при создании DTO из сущности), можно добавить кастомную логику:

```java
@Mapper(componentModel = "spring")
public abstract class UserMapper {
    @AfterMapping
    protected void afterToDto(User user, @MappingTarget UserDto userDto) {
        userDto.setCreatedDate(user.getCreatedDate());
        userDto.setLastModifiedDate(user.getLastModifiedDate());
    }
}
```

или

```java
@Mapper(componentModel = MappingConstants.ComponentModel.SPRING)  
public interface UserMapper {  
  
    @Mapping(target = "updatedAt", expression = "java(java.time.LocalDateTime.now())")  
    @Mapping(target = "createdAt", expression = "java(java.time.LocalDateTime.now())")  
    @Mapping(target = "active", constant = "true")  
    User toEntity(CreateUserDto createUserDto);  
  
    UserResponseDto toDto(User user);  
  
    @Mapping(target = "updatedAt", expression = "java(java.time.LocalDateTime.now())")  
    void updateEntity(UpdateUserDto updateUserDto, @MappingTarget User user);  
}
```


---

## **6. Итоговая таблица**

Сводная таблица аннотаций аудирования

|Аннотация|Где используется|Назначение|Пример|
|---|---|---|---|
|`@EnableJpaAuditing`|Класс конфигурации|Включает аудирование для всего приложения|`@EnableJpaAuditing`|
|`@EntityListeners`|Класс сущности|Подключает слушатели к сущности|`@EntityListeners(AuditingEntityListener.class)`|
|`@CreatedDate`|Поле сущности|Автоматически заполняется датой создания|`@CreatedDate private LocalDateTime createdDate;`|
|`@LastModifiedDate`|Поле сущности|Автоматически заполняется датой изменения|`@LastModifiedDate private LocalDateTime lastModifiedDate;`|


----
#### [[Entity Auditing - Spring Data JPA - Flashcards|Link to flashcards]]



---
### References:

