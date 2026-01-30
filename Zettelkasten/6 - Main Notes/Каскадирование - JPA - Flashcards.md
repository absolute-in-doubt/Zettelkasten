
Theory for the cards: [[Каскадирование - JPA]]

FILE TAGS: jpa

Q: Какие в JPA есть уровни каскадирования? Чем они отличаются? Где устанавливаются?
A:   
	
| Тип       | Описание                                          |
| --------- | ------------------------------------------------- |
| `PERSIST` | При `em.persist(parent)` сохраняются дети         |
| `MERGE`   | При `em.merge(parent)` сливаются дети             |
| `REMOVE`  | При `em.remove(parent)` удаляются дети            |
| `REFRESH` | При `em.refresh(parent)` обновляются дети из БД   |
| `DETACH`  | При `em.detach(parent)` отсоединяются дети        |
| `ALL`     | **Все операции сразу** (PERSIST+MERGE+REMOVE+...) |
	
Устанавливаются в аннотациях связи в entity:
	
```java
@Entity
public class Post {
    @Id private Long id;
    
    @OneToMany(mappedBy = "post", cascade = CascadeType.ALL)
    private List<Comment> comments;  // Каскад на детей
    
    @ManyToOne(cascade = CascadeType.PERSIST)  // Только сохранение
    private User author;
}
```
<!--ID: 1769513155498-->



Q: Какие существуют рекомендации по каскадированию сущностей (JPA)?
A:   
	
| Ассоциация            | Cascade           | orphanRemoval | Причина                       |
| --------------------- | ----------------- | ------------- | ----------------------------- |
| `@OneToMany` владение | `PERSIST + MERGE` | `true`        | Жизненный цикл Child привязан |
| `@ManyToOne`          | `{}` (пусто)      | `false`       | Parent независим              |
| `@ManyToMany`         | `PERSIST + MERGE` | `false`       | Только сохранение связи       |
| `@OneToOne` владение  | `ALL`             | `true`        | Полное владение Child         |
	
`+` В `@OneToOne, @OneToMany` рекомендуется ещё ставить `orphanRemoval=true`
<!--ID: 1769513172069-->
