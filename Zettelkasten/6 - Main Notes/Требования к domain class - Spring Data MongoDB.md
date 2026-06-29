
27-06-2026 16:26

Status:

Tags: [[Spring Data MongoDB]] [[Spring Data Access]] [[Spring]] [[Mongo DB]]

---
# Требования к domain class - Spring Data MongoDB


```java
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.data.mongodb.core.mapping.Field;

@Document(collection = "users") // коллекция MongoDB
public class User {

    @Id
    private String id;

    @Field("first_name") // поле документа в MongoDB
    private String firstName;

    @Field("birth_year")
    private Integer birthYear;
}
```

> [!tip] Note
> `@Id` автоматически маппит поле в `_id` в MongoDB document.


> [!note]
> Поле `id` использует тип `String`. 
> Используем `String` потому что default serialization в MongoTemplate (используется под капотом репозиториев) использует поля, имеющие имя "id" в качестве id документа.
>
> Currently, Spring Data MongoDB supports `String`, `ObjectId`, and `BigInteger` as ID types.
>
>Please see [ID mapping](https://docs.spring.io/spring-data/mongodb/reference/mongodb/template-crud-operations.html#mongo-template.id-handling) for more information about on how the `id` field is handled in the mapping layer.


Если класс помечен **~={purple}@Document=~**, Spring Data может заранее просканировать его как сущность MongoDB; это не строго обязательно для работы, но рекомендуется для нормального mapping и индексов.


- Поля могут быть immutable; Spring Data умеет работать с final-полями, `with...`-методами и all-args constructor. ~={orange}То есть даже не обязательно наличие no-args constructor.=~

- Не-персистентные поля нужно помечать `@Transient`, иначе они будут считаться частью документа


|JPA|Spring Data MongoDB|Назначение|
|---|---|---|
|`@Table(name = "...")`|`@Document(collection = "...")`|Указывает коллекцию|
|`@Column(name = "...")`|`@Field("...")`|Маппинг имени поля|
|`@Id` (`jakarta.persistence`)|`@Id` (`org.springframework.data.annotation`)|Идентификатор документа|


### Создание репозитория

```java
public interface UserRepository extends PagingAndSortingRepository<User, String> {

    // additional custom query methods go here
}
```


----
#### [[Требования к domain class - Spring Data MongoDB - Flashcards|Link to flashcards]]



---
### References:

