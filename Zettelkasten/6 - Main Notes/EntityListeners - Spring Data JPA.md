
25-05-2026 13:35

Status:

Tags: [[Spring Data JPA]] [[Spring]]

---
# EntityListeners - Spring Data JPA

**`@EntityListeners`** — аннотация в **JPA (Java Persistence API)**, которая позволяет **прикрепить слушатели (listeners)** к сущности. Эти слушатели **реагируют на события жизненного цикла сущности** (например, сохранение, обновление, удаление, загрузка) и выполняют дополнительную логику.

**~={red}Класс-слушатель не должен** быть Spring-бином (не нужно аннотировать `@Component`, `@Service` и т.д.).**=~


---

## **2. Зачем нужен `@EntityListeners`?**

- **Автоматическое заполнение полей** (например, даты создания/изменения, аудит).
- **Валидация данных** перед сохранением.
- **Логгирование изменений** сущности.
- **Синхронизация данных** с другими системами.
- **Шифрование/дешифрование** полей перед сохранением/загрузкой.

---

## **3. Как работает `@EntityListeners`?**

1. **Создаётся класс-слушатель** (implements `EntityListener` или использует аннотации типа `@PrePersist`, `@PreUpdate` и т.д.).
2. **Слушатель прикрепляется к сущности** через `@EntityListeners`.
3. **JPA вызывает методы слушателя** при наступлении соответствующих событий.

---

## **4. Основные аннотации для слушателей**

Аннотации для слушателей сущностей

|Аннотация|Описание|Когда вызывается|
|---|---|---|
|`@PrePersist`|Выполняется **перед сохранением** сущности|Перед `INSERT`|
|`@PostPersist`|Выполняется **после сохранения** сущности|После `INSERT`|
|`@PreUpdate`|Выполняется **перед обновлением** сущности|Перед `UPDATE`|
|`@PostUpdate`|Выполняется **после обновления** сущности|После `UPDATE`|
|`@PreRemove`|Выполняется **перед удалением** сущности|Перед `DELETE`|
|`@PostRemove`|Выполняется **после удаления** сущности|После `DELETE`|
|`@PostLoad`|Выполняется **после загрузки** сущности из БД|После `SELECT`|

---

## **5. Примеры использования**

---

#### 1: Автоматическое заполнение даты создания и изменения

**Слушатель:**

```java
public class AuditingListener {

    @PrePersist
    public void setCreatedDate(Object entity) {
        if (entity instanceof Auditable) {
            ((Auditable) entity).setCreatedDate(LocalDateTime.now());
            ((Auditable) entity).setLastModifiedDate(LocalDateTime.now());
        }
    }

    @PreUpdate
    public void setLastModifiedDate(Object entity) {
        if (entity instanceof Auditable) {
            ((Auditable) entity).setLastModifiedDate(LocalDateTime.now());
        }
    }
}
```

**Интерфейс `Auditable`:**

```java
public interface Auditable {
    void setCreatedDate(LocalDateTime createdDate);
    void setLastModifiedDate(LocalDateTime lastModifiedDate);
}
```

> [!warning]
> **Интерфейс `Auditable` НЕ нужен** для работы `@LastModifiedDate` и `@CreatedDate`. Он **не обязателен**, и в современных проектах его **редко реализуют**.


**Сущность:**

```java
@Entity
@EntityListeners(AuditingListener.class)
public class User implements Auditable {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private LocalDateTime createdDate;
    private LocalDateTime lastModifiedDate;

    // Геттеры и сеттеры
    @Override
    public void setCreatedDate(LocalDateTime createdDate) {
        this.createdDate = createdDate;
    }

    @Override
    public void setLastModifiedDate(LocalDateTime lastModifiedDate) {
        this.lastModifiedDate = lastModifiedDate;
    }
}
```

---

### **Пример 2: Логгирование изменений сущности**

**Слушатель:**

```java
public class LoggingListener {

    @PostPersist
    @PostUpdate
    @PostRemove
    public void logEntityChange(Object entity) {
        System.out.println("Entity " + entity.getClass().getSimpleName() + " was changed: " + entity);
    }
}
```

**Сущность:**

```java
@Entity
@EntityListeners(LoggingListener.class)
public class Product {
    @Id
    private Long id;
    private String name;
    // Геттеры и сеттеры
}
```

---

### **Пример 3: Шифрование поля перед сохранением**

**Слушатель:**

```java

public class EncryptionListener {

    @PrePersist
    @PreUpdate
    public void encryptSensitiveData(Object entity) {
        if (entity instanceof User) {
            User user = (User) entity;
            user.setPassword(encrypt(user.getPassword()));
        }
    }

    @PostPersist
    @PostUpdate
    @PostLoad
    public void decryptSensitiveData(Object entity) {
        if (entity instanceof User) {
            User user = (User) entity;
            user.setPassword(decrypt(user.getPassword()));
        }
    }

    private String encrypt(String data) {
        // Логика шифрования
        return "encrypted_" + data;
    }

    private String decrypt(String data) {
        // Логика дешифрования
        return data.replace("encrypted_", "");
    }
}
```

**Сущность:**

```java
@Entity
@EntityListeners(EncryptionListener.class)
public class User {
    @Id
    private Long id;
    private String password;
    // Геттеры и сеттеры
}
```

---

## **6. Встроенные слушатели Spring Data JPA**

Spring Data JPA предоставляет **встроенные слушатели** для упрощения работы:

### **`AuditingEntityListener`**

- **Назначение:** Автоматическое заполнение полей `@CreatedDate`, `@LastModifiedDate`, `@CreatedBy`, `@LastModifiedBy`.
- **Как подключить:**
    
- **Требует:** `@EnableJpaAuditing` на уровне конфигурации.

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class User {
    @CreatedDate
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime lastModifiedDate;
}
```

---

## **7. Множественные слушатели**

К одной сущности можно прикрепить **несколько слушателей**:

```java
@Entity
@EntityListeners({AuditingListener.class, LoggingListener.class, EncryptionListener.class})
public class User {
    // Поля и методы
}
```

---

## **8. Глобальные слушатели**

Если нужно, чтобы слушатель работал **для всех сущностей**, можно зарегистрировать его **глобально** через `persistence.xml` или программно:

```java
@Configuration
public class JpaConfig {
    @Bean
    public JpaVendorAdapter jpaVendorAdapter() {
        HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
        vendorAdapter.setEntityListeners(new AuditingListener(), new LoggingListener());
        return vendorAdapter;
    }
}
```


---

## **9. Особенности и ограничения**

1. **Слушатели не должны быть `@Component`**
    
    - **~={red}Класс-слушатель не должен быть Spring-бином (не нужно аннотировать `@Component`, `@Service` и т.д.).=~
    - JPA создаёт экземпляры слушателей **самостоятельно**.
2. **Слушатели должны иметь публичный конструктор без аргументов**
    
    - JPA вызывает конструктор без параметров.
3. **Слушатели не должны зависеть от Spring-контекста**
    
    - Если нужно использовать Spring-бины внутри слушателя, можно **внедрять их статически** или через `ApplicationContext`.
4. **Порядок выполнения слушателей**
    
    - Если к сущности прикреплено несколько слушателей, **порядок их вызова не гарантирован**.
    - Для контроля порядка можно использовать **один слушатель с несколькими методами**.
5. **Производительность**
    
    - Слишком сложная логика в слушателях может **замедлить работу** с базой данных.

---

## **10. Интеграция с другими технологиями**

### **1. Spring Security**

Можно использовать **`SecurityContextHolder`** в слушателе, чтобы получить текущего пользователя:

```java
public class SecurityAuditingListener {

    @PrePersist
    @PreUpdate
    public void setAuditor(Object entity) {
        if (entity instanceof Auditable) {
            String username = SecurityContextHolder.getContext().getAuthentication().getName();
            ((Auditable) entity).setLastModifiedBy(username);
        }
    }
}
```



----
#### [[EntityListeners - Spring Data JPA - Flashcards|Link to flashcards]]



---
### References:

