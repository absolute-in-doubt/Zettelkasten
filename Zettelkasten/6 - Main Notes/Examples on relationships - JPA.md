
04-11-2025 16:28

Status:

Tags:

---
# Examples on relationships - JPA

**A onedirectional association via a join table**

```java
@Entity
class Patient {

    @OneToMany
    private Collection<Vehicle> vehicles = new ArrayList<Vehicle>();

}

@Entity
class Vehicle {

}
```

**Two onedirectional associations via a join table**

```java
@Entity
class Patient {

    @OneToMany
    private Collection<Vehicle> vehicles = new ArrayList<Vehicle>();

}

@Entity
class Vehicle {

    @ManyToOne(fetch = FetchType.LAZY)
    private Patient patient;

}
```

In detail: [What is the "owning side" in an ORM mapping?](https://stackoverflow.com/questions/2749689/what-is-the-owning-side-in-an-orm-mapping/21068644#21068644)

**A onedirectional association via a foreign key**

```java
@Entity
class Patient {

    @OneToMany
    @JoinColumn
    private Collection<Vehicle> vehicles = new ArrayList<Vehicle>();

}

@Entity
class Vehicle {

}
```

**A bidirectional association via a foreign key**

```java
@Entity
class Patient {

    @OneToMany(mappedBy = "patient")
    private Collection<Vehicle> vehicles = new ArrayList<Vehicle>();

}

@Entity
class Vehicle {

    @ManyToOne(fetch = FetchType.LAZY)
    private Patient patient;

}
```

We don't need to use `@JoinColumn` on the `Vehicle` side, Hibernate assumes it by default. Sometimes I use it just to stress it out (another case, when we want to specify a join column name).

```java
    @Entity
    class Vehicle {
    
        @ManyToOne(fetch = FetchType.LAZY)
        @JoinColumn
        private Patient patient;
    
    }
```

**A bidirectional association via a foreign key with a foreign column name specification**

```java
@Entity
class Patient {

    @OneToMany(mappedBy = "patient")
    private Collection<Vehicle> vehicles = new ArrayList<Vehicle>();

}

@Entity
class Vehicle {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name="patient_id")
    private Patient patient;

}
```

This is the basic starting point of using `@JoinColumn`.

To verify that the foreign key(`patient_id` in the `Vehicle` table) is really mapped in the patients table you can use `@JoinColumn(nullable = false)`

```java
@Entity
class Vehicle {

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name="patient_id", nullable = false)
    private Patient patient

}
```

----
#### [[Examples on relationships - JPA - Flashcards|Link to flashcards]]



---
### References:

