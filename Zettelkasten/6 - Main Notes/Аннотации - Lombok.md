
13-02-2026 15:36

Status:

Tags: [[Lombok]] [[Java+]]

---
# Аннотации - Lombok



### **Конструкторы**

|Аннотация|Что генерирует|
|---|---|
|`@NoArgsConstructor`|`public Class() {}`|
|`@AllArgsConstructor`|Конструктор со всеми полями|
|`@RequiredArgsConstructor`|Только для `final`/`@NonNull` полей|

#### **Геттеры/Сеттеры**

|Аннотация|Что генерирует|Применение|
|---|---|---|
|`@Getter`|`getField()`|поле/класс|
|`@Setter`|`setField()`|поле/класс|
|`@Getter @Setter`|геттер+сеттер|поле/класс|
|`@Data`|`@Getter/@Setter + toString/equals/hashCode +` @RequiredArgsConstructor`|**класс**|


#### **Builder & Utility**

|Аннотация|Что делает|
|---|---|
|`@Builder`|`Class.builder().field().build()`|
|`@Builder(toBuilder = true)`|`existing.toBuilder().field().build()`|
|`@ToString`|`toString()`|
|`@EqualsAndHashCode`|`equals()` + `hashCode()`|
|`@NonNull`|Null-check + NPE|

#### **Утилиты**

| Аннотация              | Что делает                          |
| ---------------------- | ----------------------------------- |
| **~={yellow}@Slf4j=~** | `private static final Logger log`   |
| `@SneakyThrows`        | `@SuppressWarnings("unchecked")`    |
| `val`                  | `final auto` (локальная переменная) |
| `var`                  | `auto` (переменная)                 |

> [!warning]
> сама зависимость SLF4J **не подтягивается автоматически** с Lombok.

#### ✅ **Золотая комбинация для Entity**

```java
@Entity
@Data                // getters/setters/toString/equals/hashCode
@NoArgsConstructor   // для Hibernate
@AllArgsConstructor
@Builder             // fluent создание
public class User {
    private Long id;
    private String name;
}
```

## ⚠️ **Осторожно**

```java
❌ НЕ используй @Data на JPA Entity с bidirectional связями
✅ Используй @Getter/@Setter/@Builder отдельно
❌ НЕ ставь @Data + @Id — конфликты equals/hashCode
```



xml

`<dependency>     <groupId>org.projectlombok</groupId>    <artifactId>lombok</artifactId>    <optional>true</optional> </dependency>`

----
#### [[Аннотации - Lombok - Flashcards|Link to flashcards]]



---
### References:

