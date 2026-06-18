
11-06-2026 18:14

Status:

Tags: [[MapStruct]] [[Java+]]

---
# Маппинг вложенных полей - MapStruct


**~={red}?!=~** MapStruct не использует SpEL, но поддерживает маппинг вложенных полей через точку, почти как Spring expressions.


  `source = "item.id"` will automatically: 
  
   1. Get `item` from `OrderItems`                                                                       
   2. Extract `id` from the `Item` object
   3. Map it to `itemId` in the DTO 
   
  Same for `item.name` → `itemName` and `item.price` → `itemPrice`. No SpEL needed — MapStruct's expression language handles nested fields natively. 
  
  **The generated code will include null checks:** if `entity.getItem()` is `null`, the target fields will be null. 


```java
public interface OrderItemsMapper {  
	  
    @Mapping(target = "orderItemId", source = "id")  
    @Mapping(target = "itemId", source = "item.id")  
    @Mapping(target = "itemName", source = "item.name")  
    @Mapping(target = "itemPrice", source = "item.price")  
    OrderItemRequestDto toDto(OrderItems entity);  
}
```



```java
public record OrderItemResponseDto(  
        Long orderItemId,  
        Long itemId,  
        String itemName,  
        String itemPrice,  
        Long quantity  
) {  
}
```


```java
@Table(name = "order_items")  
@Entity  
@Data  
@NoArgsConstructor  
public class OrderItems {  
	
    @SequenceGenerator(name = "order_items_gen", sequenceName = "order_items_seq")  
	  
    @Id  
    @GeneratedValue(generator = "order_items_gen")  
    private Long id;  
	  
    @ManyToOne  
    @JoinColumn(name = "order_id", referencedColumnName = "id")  
    private Order order;  
	  
    @ManyToOne  
    @JoinColumn(name="item_id", referencedColumnName = "id")  
    private Item item;  
	  
    @Column(name="quantity")  
    private Long quantity;  
}
```

----
#### [[Маппинг вложенных полей - MapStruct - Flashcards|Link to flashcards]]



---
### References:

