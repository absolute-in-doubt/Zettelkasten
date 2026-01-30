
11-01-2026 14:40

Status:

Tags: [[Hibernate & JPA]] [[Java+]]

---
# Lazy, Eager загрузка - JPA


```java
@Entity
public class Order {

    // коллекция: по умолчанию LAZY, но зададим явно
    @OneToMany(mappedBy = "order", fetch = FetchType.LAZY)
    private Set<OrderItem> items;

    // одиночная связь: по умолчанию EAGER, но можно сделать LAZY
    @ManyToOne(fetch = FetchType.LAZY)
    private Customer customer;

    // пример явного EAGER
    @OneToOne(fetch = FetchType.EAGER)
    private Invoice invoice;
}
```


## Значения по умолчанию

- `@OneToMany` → `FetchType.LAZY`
    
- `@ManyToMany` → `FetchType.LAZY`
    
- `@ManyToOne` → `FetchType.EAGER`
    
- `@OneToOne` → `FetchType.EAGER`


> [!warning]
> Если ленивые связи не были инициализированы до конца транзакции, после её завершения при отсоединении сущностей они останутся неинициализированными, а попытка их загрузить приведёт к `LazyInitializationException` (в Hibernate) или аналогичной ошибке, а не к повторной загрузке.
>
> **Что происходит под капотом:**
> - Пока транзакция активна и `EntityManager`/`Session` открыт, ленивые связи представлены прокси и могут быть подгружены при первом обращении к ним.​
>    
>- В момент коммита транзакции persistence context закрывается, сущности становятся **detached**, и прокси больше не может сходить в БД за данными.
>    
>- Если ленивое поле ни разу не дернули внутри открытого контекста, оно так и останется в виде неинициализированного прокси; попытка доступа уже _после_ закрытия контекста вызовет исключение, а не «null → загрузка»


----
#### [[Lazy, Eager загрузка - JPA - Flashcards|Link to flashcards]]



---
### References:

