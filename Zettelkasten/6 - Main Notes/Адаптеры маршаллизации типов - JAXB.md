
06-11-2025 11:55

Status: #baby 

Tags: [[JAXB]] [[Java+]]

---
# Адаптеры маршаллизации типов - JAXB

Используется для кастомной логики маршаллизации/демаршаллизации типов. 

Пример (Адаптер маршаллизации/демаршаллизации типа Date):
```java
public class DateTimeAdapter extends XmlAdapter<String, Date> {  
    @Override  
    public Date unmarshal(String v) throws Exception {  
        return Date.valueOf(v);  
    }  
    @Override  
    public String marshal(Date v) throws Exception {  
        return new SimpleDateFormat("yyyy-MM-dd").format(v);  
    }  
}
```

Он используется при маршаллизации/демаршаллизации полей классов типа Date. Над полем класса указывается аннотация:
~={purple}@XmlJavaTypeAdapter(`Class<? extends XmlaAdapter<A, B>>`)=~

Пример:
```java
@XmlType(name = "employee")  
@XmlAccessorType(XmlAccessType.FIELD)  
@XmlRootElement  
public class Employee {  

    @XmlTransient  
    private int employeeId;  
    
    private String emplName;  
      
    @Temporal(TemporalType.DATE)  
    @XmlJavaTypeAdapter(DateTimeAdapter.class)  
    private Date emplDob;
```



----
#### [[Адаптеры маршаллизации типов - JAXB - Flashcards|Link to flashcards]]



---
### References:

