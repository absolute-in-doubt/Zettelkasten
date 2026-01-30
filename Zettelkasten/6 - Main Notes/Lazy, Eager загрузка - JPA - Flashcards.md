
Theory for the cards:  [[Lazy, Eager загрузка - JPA]]

FILE TAGS: jpa

Q: Если на момент завершения транзакции связанные lazily объекты так и не были загружены, то они так и останутся null в объектах после того как транзакция завершится и entities will be detached?
A: Да, в общем случае так и будет: если ленивые связи не были инициализированы до конца транзакции, после её завершения при отсоединении сущностей они останутся неинициализированными, а попытка их загрузить приведёт к `LazyInitializationException` (в Hibernate) или аналогичной ошибке, а не к повторной загрузке.
<!--ID: 1768296882670-->

Q: Как можно установить тип подтягивания связанных сущностей в JPA? Какие типы подтягивания зависимостей в JPA по умолчанию?
A: Это делается с помощью параметра fetch аннотаций отношений:
	
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
	
**Значения по умолчанию**
	
- `@OneToMany` → `FetchType.LAZY`
    
- `@ManyToMany` → `FetchType.LAZY`
    
- `@ManyToOne` → `FetchType.EAGER`
    
- `@OneToOne` → `FetchType.EAGER`
<!--ID: 1769513531005-->
