
Theory for the cards: [[@ManyToMany - JPA]]

FILE TAGS: jpa

Q: Почему связь ManyToMany считается опасной?
A: - Её сложно расширять (например, добавить к связи дополнительные поля — дату записи на курс, статус и т.д.).
	
- Если нужно хранить данные о самой связи, почти всегда лучше заменить `@ManyToMany` на две `@OneToMany` через промежуточную сущность (например, `Enrollment` между `Student` и `Course`).
<!--ID: 1769512676544-->


Q: Как в JPA реализуется связ Entities вида Many to Many? Приведи пример.
A: В JPA Many To Many реализуется с помощью:
	- В первом Entity: `@ManyToMany + @JoinTable`
	- В другом: `ManyToMany( mappedBy="...")`
	
Пример:
	
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
<!--ID: 1769512676556-->
