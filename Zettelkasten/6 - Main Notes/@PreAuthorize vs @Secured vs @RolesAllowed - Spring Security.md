
26-03-2026 11:22

Status:

Tags: [[Spring Security]]

---
# @PreAuthorize vs @Secured vs @RolesAllowed - Spring Security


#### @Secured

**~={purple}@Secured(`"ROLE_ADMIN"`)=~** - ограничивает доступ только для тех, у кого есть указанные роли. 

Применяется на уровне методов в сервисах или контроллерах и требует включения поддержки через **~={purple}@EnableGlobalMethodSecurity(`securedEnabled = true`)=~**.

> [!warning] Note
> Не поддерживает SpEL (Spring Expression Language), в отличие от **~={purple}@PreAuthorize=~** или **~={purple}@PostAuthorize=~**, делая её проще для базовых сценариев.

## Сравнение с альтернативами

| Аннотация       | Пакет                                                      | Особенности                          |
| --------------- | ---------------------------------------------------------- | ------------------------------------ |
| `@Secured`      | `org.springframework.security.access.annotation.Secured`   | Роли строкой, без SpEL               |
| `@RolesAllowed` | `javax.annotation.security.RolesAllowed` (JSR-250)         | Аналог `@Secured`, стандарт Java EE  |
| `@PreAuthorize` | `org.springframework.security.access.prepost.PreAuthorize` | Полная поддержка SpEL, более гибкая  |

Пример: `@Secured({"ROLE_USER", "ROLE_ADMIN"})` разрешает доступ пользователям с любой из этих ролей.

----
#### [[@PreAuthorized vs @Secured vs @RolesAllowed - Spring Security - Flashcards|Link to flashcards]]



---
### References:

