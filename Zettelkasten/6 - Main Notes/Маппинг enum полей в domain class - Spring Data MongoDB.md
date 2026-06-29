
27-06-2026 18:01

Status:

Tags: [[Spring Data MongoDB]] [[Spring Data Access]] [[Spring]]

---
# Маппинг enum полей в domain class - Spring Data MongoDB

В Spring Data MongoDB прямого аналога JPA `@Enumerated` нет: `@Enumerated(EnumType.STRING/ORDINAL)` для MongoDB не поддерживается как стандартный механизм, и команда Spring Data советует использовать собственный `Converter` для enum.


**Что есть вместо этого**

- ~={orange}По умолчанию Spring Data MongoDB мапит enum в строку=~, то есть обычно сохраняется `Enum.name()`.
    
- Если нужен числовой формат, можно использовать `@Field(targetType = FieldType.INT32)` или собственный converter, который пишет `Integer`.
    
- Для кастомного текста вроде `type_a` / `type_b` нужен `MongoCustomConversions` с `Converter<Enum, String>` и `Converter<String, Enum>`.

----
#### [[Маппинг enum полей в domain class - Spring Data MongoDB - Flashcards|Link to flashcards]]



---
### References:

