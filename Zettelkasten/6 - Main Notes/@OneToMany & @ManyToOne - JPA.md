
29-10-2025 07:55

Status: #baby

Tags: [[Hibernate & JPA]]

---
# @OneToMany & @ManyToOne - JPA

**Reminder:**
	Владелец отношения - Child, т.к. он имеет FOREIGN KEY, указывающий на Parent запись (из другой таблицы). Пдробнее тут ([[One to Many relationship - БД]])



![[Pasted image 20251029075652.png]]


### Аннотации

#### @ManyToOne

В БД вместо этого поля будет лежать PRIMARY KEY связанного объекта. 


#### @JoinColumn(name="person_id", referencedColumnName="id")

тут "person_id" -  имя колонки, содержащей PK объекта Person в таблице Item.

"id" - название колонки в родительской таблице, т.е. он ссылается на колонку "id" в таблице Person.



#### @OneToMany(mappedBy="owner")

Указывается в Parent классе (На который указывает FK).

тут "owner" - имя ПОЛЯ в owner (Child) классе.


---
### Examples 

##### ONE DIRECTIONAL ONE TO MANY


`1st type` - об отношении знает только Parent (~={red}**он не владелец отношения**=~):
```java
import jakarta.persistence.*;
import java.util.List;

@Entity
public class Department {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(cascade = CascadeType.ALL)
    @JoinColumn(name = "department_id") // 👈 создаёт внешний ключ в Employee
    private List<Employee> employees;

    // Getters, setters, toString
}
```

```java
import jakarta.persistence.*;

@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    // 👈 Нет ссылки на Department — односторонняя связь
}
```


`2nd type` - об отношении знает только Child (он владеет отношением):
```java
import jakarta.persistence.*;
import java.util.List;

@Entity
public class Department {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
	
    // 👈 Нет ссылки на всех связанных Employee — односторонняя связь
	
    // Getters, setters, toString
}
```

```java
import jakarta.persistence.*;

@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    
    @ManyToOne
    @JoinColumn(name = "department_id", referencedColumnName="id")  // есть ссылка на Department
    private Department department;

}
```

##### BIDIRECTIONAL ONE TO MANY

```java
import jakarta.persistence.*;
import java.util.List;

@Entity
public class Department {
	
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
	
    private String name;
	
    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Employee> employees;
	
    // Getters, setters, toString
}
```


```java
import jakarta.persistence.*;

@Entity
public class Employee {
	
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
	
    private String name;
	
    @ManyToOne
    @JoinColumn(name = "department_id", referencedColumnName="id")
    private Department department;
	
    // Getters, setters, toString
}
```

### Работа со связанными объектами



> [!warning]
> Если мы работаем с BIDIRECTIONAL ONE TO MANY:
> 
> При добавлении Child объекта в БД. 
> ```java
> session.getTransaction().begin();  
 > 
>Person person = (Person) session.createQuery("FROM Person WHERE name = 'Adam'").getResultList().get(0);  
 > 
>Item item = new Item("GBC badge");  
>item.setPerson(person);  
 > 
>session.persist(item);  
 > 
>person.getItems();  //автоматически НЕ подтянет новодобавленный Item, т.к. это Person уже был запрошен и его кэш хранится в Context, List<Item> подтянется оттуда
 > 
>person.getItems().add(item); //надо вручную добавить, если хотим далее работать с этим списком. Если же не будем далее с ним работать -> можно не добавлять. На сохранение в БД это не влияет.  
 > 
>System.out.println(item.getId());
> ```
> 
>~={red}**При BIDIRECTIONAL ONE TO MANY считается хорошей практикой всё же добавлять `person.getItems().add(item);` -> значение в таблице БД и в кэше Hibernate совпадают.**=~



> [!note]
> Вызывать геттер для получения объекта, связанного с merged object нужно только внутри транзакции. Т.к. только при вызове геттера выполняется запрос на получение связанных объектов. Т.е. они подтягиваются ==lazily==
> Код транзакции:
> ```java
> session.getTransaction().begin();  
>
>Person person = session.find(Person.class, 15);  
>System.out.println(person);  
>for(Item item : person.getItems()){  
>    System.out.println(item);  
>}  
  >
>session.getTransaction().commit();
> ```
> Output (включено отображение prepared statements by Hibernate):
> ```
> Hibernate: select p1_0.id,p1_0.age,p1_0.email,p1_0.name from Person p1_0 where p1_0.id=?
> 
>Person{id=15, name=Adam, age=35, email=wiccaphase@gmail.com}
>
>Hibernate: select i1_0.person_id,i1_0.id,i1_0.item_name from Item i1_0 where i1_0.person_id=?
>
>Item{id=22, person=Person{id=15, name=Adam, age=35, email=wiccaphase@gmail.com}, name='Guitar'}
>Item{id=23, person=Person{id=15, name=Adam, age=35, email=wiccaphase@gmail.com}, name='Mic'}
>Item{id=24, person=Person{id=15, name=Adam, age=35, email=wiccaphase@gmail.com}, name='Golden record award'}
> ```



----
#### [[@OneToMany & @ManyToOne - JPA - Flashcards|Link to flashcards]]



---
### References:

- [[One to Many relationship - БД]]