
27-06-2026 15:52

Status:

Tags: [[Spring Data Access]] [[Spring]]

---
# Как Spring Data определяет, что сущность new - Spring Data Access

The following table describes the strategies that Spring Data offers for detecting whether an entity is new:

Table 1. Options for detection whether an entity is new in Spring Data


| `@Id`-Property inspection (the default) | By default, Spring Data inspects the identifier property of the given entity. If the identifier property is `null` or `0` in case of primitive types, then the entity is assumed to be new. Otherwise, it is assumed to not be new.                                                                                                                                                                                     |
| --------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `@Version`-Property inspection          | If a property annotated with `@Version` is present and `null`, or in case of a version property of primitive type `0` the entity is considered new. If the version property is present but has a different value, the entity is considered to not be new. If no version property is present Spring Data falls back to inspection of the identifier property.                                                            |
| Implementing `Persistable`              | If an entity implements `Persistable`, Spring Data delegates the new detection to the `isNew(…)` method of the entity. See the [Javadoc](https://docs.spring.io/spring-data/commons/reference/4.1/api/java/org/springframework/data/domain/Persistable.html) for details.<br><br>_Note: Properties of `Persistable` will get detected and persisted if you use `AccessType.PROPERTY`. To avoid that, use `@Transient`._ |



----
#### [[Как Spring Data определяет, что сущность new - Spring Data Access - Flashcards|Link to flashcards]]



---
### References:

