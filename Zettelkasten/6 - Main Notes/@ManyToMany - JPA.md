
04-01-2026 17:56

Status:

Tags:

---
# @ManyToMany - JPA



На уровне БД это три таблицы:

- `student(id, name, ...)`
- `course(id, title, ...)`
- `student_course(student_id, course_id)` — join‑таблица с двумя внешними ключами.


```java
@Entity
public class Student {
    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @ManyToMany
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private Set<Course> courses = new HashSet<>();
}


@Entity
public class Course {
    @Id
    @GeneratedValue
    private Long id;

    private String title;

    @ManyToMany(mappedBy = "courses")
    private Set<Student> students = new HashSet<>();
}
```


- `Student.courses` — owning side (`@JoinTable` указана здесь).
- `Course.students` — inverse side (`mappedBy = "courses"`).
- Join‑таблица `student_course` создаётся/обновляется по изменениям коллекции на стороне `Student`.



### Часто `@ManyToMany` считают «опасной» связью для сложных доменных моделей:

- Её сложно расширять (например, добавить к связи дополнительные поля — дату записи на курс, статус и т.д.).

- Если нужно хранить данные о самой связи, почти всегда лучше заменить `@ManyToMany` на две `@OneToMany` через промежуточную сущность (например, `Enrollment` между `Student` и `Course`).

----
#### [[@ManyToMany - JPA - Flashcards|Link to flashcards]]



---
### References:

