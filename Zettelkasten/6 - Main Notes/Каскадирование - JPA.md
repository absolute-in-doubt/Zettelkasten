
29-10-2025 13:10

Status: #baby

Tags: [[Hibernate & JPA]]

---
# Каскадирование - JPA

Обычно настраивается на Parent сущности. Но можно и на обоих.

Нальзя, чтобы поле связанного объекта (FK) одновременно являлся PK:

```java
@Entity  
@Table(name="session")  
public class Session {  
    //@Id  - неправильно, т.к. он уже FK
    @ManyToOne    
    @JoinColumn(name="user_id", referencedColumnName = "user_id") 
    private User user;  
    @Id // - правильно
    @Column(name="refresh_token_hash")  
    private String refreshTokenHash;  
 ...   
}    
    
@Entity  
@Table(name="\"user\"")  
public class User {   
...
    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)  
    private List<Session> sessions;
...
}
```

~={pink}Hibernate просто тупит. Ну, точнее, причины я не знаю.=~





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


> [!warning]
> Чтобы связь создалась необходимо, чтобы владелец отношения (тот, кто хранит FK) получил ссылку на Parent объект. 
> 
> В данном случае отношение создаётся на этом этапе:
> ```java
> testCascadingItem.setPerson(person);  
> ```







| CascadeType | Что каскадируется         | Пример                                                                    |
| ----------- | ------------------------- | ------------------------------------------------------------------------- |
| `PERSIST`   | `EntityManager.persist()` | При сохранении родителя автоматически сохраняются новые дочерние сущности |
| `MERGE`     | `EntityManager.merge()`   | При обновлении родителя обновляются дочерние сущности                     |
| `REMOVE`    | `EntityManager.remove()`  | При удалении родителя удаляются дочерние сущности                         |
| `REFRESH`   | `EntityManager.refresh()` | При обновлении сущности из БД обновляются и дочерние                      |
| `DETACH`    | `EntityManager.detach()`  | При отсоединении родителя от Persistence Context отсоединяются и дочерние |
| `ALL`       | Все перечисленные выше    | Эквивалент `PERSIST + MERGE + REMOVE + REFRESH + DETACH`                  |

**~={red}?!=~** Никакие уровни каскадирования не включают в себя другие, кроме `ALL`.

----
#### [[Каскадирование - JPA - Flashcards|Link to flashcards]]



---
### References:

- [[FOREIGN KEY - SQL#Каскадирование]]
- 