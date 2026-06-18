
26-11-2025 12:30

Status:

Tags: [[Hibernate & JPA]] [[Java+]]

---
# FK как PRIMARY KEY - Hibernate

Обычно он работает нормально, просто обозначаем связанный объект как ~={yellow}@Id=~ и всё:

 ```java
 @Id  
@ManyToOne
@JoinColumn(name = "calculation_id", referencedColumnName = "calculation_id")  
private CalculationValue calculationValue;
 ```

---

### Когда нужно иначе

Если используем Spring Data Jpa, то PRIMARY KEY, т.е. ~={yellow}@Id=~ у Entity должен быть базовым типом. 

- Используется для **shared primary key associations** — когда дочерняя сущность должна иметь тот же первичный ключ, что и родительская.

Для этого разделяем наш FK на базоывй тип и связанный с ним объект, используя ~={purple}@MapsId=~ (такж используется для связи с полем в @EmbeddedId для [[Composite PRIMARY KEY - Hibernate|COMPOSITE PRIMARY KEY]]):

```java
@Id  
@Column(name="calculation_id")  
private UUID calculationId;  
  
@ManyToOne(fetch = FetchType.LAZY) //чтобы не тягать лишний раз, если нужен только id  
@MapsId  
@JoinColumn(name = "calculation_id", referencedColumnName = "calculation_id")  
private CalculationValue calculationValue;
```


Репозиторий:
```java
@Repository  
public interface FilingRepository extends JpaRepository<Filing, UUID> {}
```


---
## ⚠️ Нюансы

##### 1. **Не сетить PK вручную** При `@MapsId` дочерняя сущность получает PK из связанного родителя.

```java
@Entity
@Table(name="label_value")
public class LabelValue {
    @Id
    @Column(name="calculation_id")
    private UUID calculationId;

    @OneToOne
    @MapsId
    @JoinColumn(name = "calculation_id")
    private CalculationValue calculationValue;

    @Column(name="value")
    private String value;
}

//===========================================================

label.setCalculationValue(calculationValue); // PK подтянется автоматически
label.setCalculationId(calculationValue.setCalculationId()); //так не надо

```

→ Если вручную проставить `calculationId`, возможны ошибки (`DuplicateKeyException`, `OptimisticLockException`).


##### 2. **Каскадирование**

- Не обязательно использовать `cascade`, но удобно:
	
	- Если `cascade = CascadeType.PERSIST` на родителе, то при сохранении дочерней сущности Hibernate автоматически сохранит родителя.
	
- Без каскада нужно явно сохранять родителя перед дочерней.


##### 3. **Загрузка данных**

- При `@MapsId` Hibernate всегда ожидает, что связь существует.
	
- ~={orange}Если дочерняя сущность без родителя — будет ошибка.=~


##### 4. **Удаление**

- При удалении родителя дочерняя сущность тоже должна удаляться (обычно через `cascade = REMOVE`).


##### 5. **Persistence Context**

- Важно сохранять только один объект с данным PK.
	
- Если сначала сохранить родителя, а потом дочернего с тем же PK, Hibernate может кинуть `DuplicateKeyException`.


---
### Examples:

Это правильно:
```java
RecordInfo record = new RecordInfo();  
UUID recordId = UUID.randomUUID();  
record.setRecordId(recordId);  
record.setCIK("0000320193");  
record.setCompanyName("Apple Inc.");  
  
recordRepo.save(record);  
record = recordRepo.findByRecordId(recordId);  
  
LabelValue label = new LabelValue();  
label.setRecordInfo(record);  
String gaapName = "us-gaap:OperatingIncomeLoss";  
label.setGaapName(gaapName);  
label.setValue("some value");  
  
  
labelRepo.save(label);
```

Это не правильно:
```java
RecordInfo record = new RecordInfo();  
UUID recordId = UUID.randomUUID();  
record.setRecordId(recordId);  
record.setCIK("0000320193");  
record.setCompanyName("Apple Inc.");  
  
recordRepo.save(record);  
//record = recordRepo.findByRecordId(recordId);  
// типо, save озвращает сущность, которая managed??? 
// БРЕД 
  
LabelValue label = new LabelValue();  
label.setRecordInfo(record);  
String gaapName = "us-gaap:OperatingIncomeLoss";  
label.setGaapName(gaapName);  
label.setValue("some value");  
  
  
  
labelRepo.save(label);
```


----
#### [[FK как PRIMARY KEY - Hibernate - Flashcards|Link to flashcards]]



---
### References:

