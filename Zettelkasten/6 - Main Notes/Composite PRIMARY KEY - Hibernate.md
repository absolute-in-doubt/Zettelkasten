
25-11-2025 20:30

Status:

Tags: [[Hibernate & JPA]] [[Java+]]

---
# Composite PRIMARY KEY - Hibernate

Создаётся комбинацией аннотаций ~={purple}@Embeddable=~ + ~={purple}@EmbeddedId=~:

Создаётся отдельный класс для ключа:
```java
@Embeddable
public class LabelValueId implements Serializable {
    @Column(name = "name")   // колонка name в БД
    private String gaapName;
	
    @Column(name = "record_id")
    private UUID recordId;
	
    @Column(name = "time")
    private Instant time;
	
    // обязательно: equals() и hashCode()
    @Override
    public boolean equals(Object o) { ... }
    @Override
    public int hashCode() { ... }
}
```


> [!note]
> `@Embeddable` — это просто контейнер для набора колонок, которые вместе образуют ключ.
>
> Внутри него **нельзя описывать отношения (**`@ManyToOne`**,** `@OneToOne`**)**, потому что PK должен быть представлен только примитивными/базовыми значениями.


В Entity указываем с помощью ~={purple}@EmbeddedId=~:

```java
@Entity
@Table(name = "label_value")
public class LabelValue {

    @EmbeddedId
    private LabelValueId id;

    @Column(name = "value")
    private String value;

    // связи можно описывать через @MapsId
    @ManyToOne
    @MapsId("recordId") // поле из LabelValueId
    @JoinColumn(name = "record_id")
    private RecordInfo recordInfo;
}
```

> [!warning]
> Указываем `record_id`(FK) в классе-композитном ключе. А отношение `recordInfo` показываем в самом Entity

> [!note]
> В JPA/Hibernate ~={red}**не рекомендуется дублировать поля композитного ключа и в** `@Embeddable`**, и в самой** `@Entity`=~ — это приводит к путанице и конфликтам при генерации схемы и при маппинге.

----
#### [[Composite PRIMARY KEY - Hibernate - Flashcards|Link to flashcards]]



---
### References:

