
29-10-2025 13:10

Status: #baby

Tags: [[Hibernate & JPA]]

---
# Каскадирование - JPA

Обычно настраивается на Parent сущности. Но можно и на обоих.








### Example

```java
@Entity  
@Table(name="Person")  
public class Person {  
  
    @Id  
    @Column(name="id")  
    @GeneratedValue(strategy=GenerationType.IDENTITY)  
    private int id;  
  
    @Column(name="name")  
    private String name;  
  
    @Column(name="age")  
    private int age;  
  
    @Column(name="email")  
    private String email;  
  
    @OneToMany(mappedBy="person", cascade=CascadeType.PERSIST)  
    private List<Item> items;

	//getters, setters,constructors    
}
```

```java
@Entity  
@Table(name="Item")  
public class Item {  
    @Id  
    @Column(name="id")  
    @GeneratedValue(strategy= GenerationType.IDENTITY)  
    private int id;  
  
    @ManyToOne    // 👈 тут не добавили каскадирование
    @JoinColumn(name="person_id", referencedColumnName="id")  
    private Person person;  
  
    @Column(name="item_name")  
    private String name;  
  
    public Item(){};  
  
    public Item(String name){  
        this.name = name;  
    }
```

Пример кода, где используется это каскадирование:
```java
session.getTransaction().begin();  
  
Person person = new Person("Kane", 49, "harrykane@gmail.com");  
  
Item testCascadingItem = new Item("ball");  
testCascadingItem.setPerson(person);  
  
person.getItems().add(testCascadingItem);  
session.persist(person);   //testCascadingItem будет сохранён автоматически
  
session.getTransaction().commit();
```


----
#### [[Каскадирование - JPA - Flashcards|Link to flashcards]]



---
### References:

- [[FOREIGN KEY - SQL#Каскадирование]]
- 