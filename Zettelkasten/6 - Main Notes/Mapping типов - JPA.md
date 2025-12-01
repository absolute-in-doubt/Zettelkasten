
25-11-2025 15:22

Status: #child 

Tags: [[Hibernate & JPA]] [[Java+]]

---
# Mapping типов - JPA

Реализуем интерфейс `AttributeConverter<Исходный тип, Конечный тип>`:

```java
import jakarta.persistence.AttributeConverter;
import jakarta.persistence.Converter;
import java.util.UUID;

@Converter(autoApply = true) // autoApply = true значит, что конвертер применяется ко всем UUID
public class UUIDStringConverter implements AttributeConverter<UUID, String> {

    @Override
    public String convertToDatabaseColumn(UUID attribute) {
        return attribute == null ? null : attribute.toString();
    }

    @Override
    public UUID convertToEntityAttribute(String dbData) {
        return dbData == null ? null : UUID.fromString(dbData);
    }
}
```


### Применение:

- **Ко всем полям (классам в проекте)**: `@Converter(autoApply = true)` - на самом Маппер классе

- **Применение вручную**: - устанавливаем на конкретном поле:

```java
@Convert(converter = UUIDStringConverter.class)
private UUID recordId;

```


----
#### [[Mapping типов - JPA - Flashcards|Link to flashcards]]



---
### References:

