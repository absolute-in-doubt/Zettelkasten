
17-04-2026 20:38

Status:

Tags: [[Hibernate & JPA]] [[Java+]]

---
# кейс FK является PK - JPA


Всё работает точно также, только в @MapsId указываем имя поля из составного ключа, которое используем.

```java
@Getter  
@Setter  
@Entity  
@Table(name = "price_bar_values")  
public class PriceBarValue {  
    @Embeddable  
    public static class PriceBarId {  
        private LocalDateTime time; //начало бара  
        private String ticker;  
        @JdbcTypeCode(SqlTypes.NAMED_ENUM)  
        @Enumerated(EnumType.STRING)  
        private PriceBarTimeFrame timeframe;  
    }  
  
    @EmbeddedId  
    private PriceBarId priceBarId;  
      
    @ManyToOne  
    @MapsId("ticker")  
    @JoinColumn(name="ticker", referencedColumnName = "ticker")  
    private Company company;
```

----
#### [[кейс FK является PK - JPA - Flashcards|Link to flashcards]]



---
### References:

