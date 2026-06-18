
08-11-2025 14:05

Status:

Tags: [[Spring]]

---
# Custom repository methods - Spring Data


В интерфейсе, расширяющем `JpaRepository<E, K>` просто прописываем сигнатуру метода - Spring Data сам её реализует.

### Конвенция именования кастомных методов (реализуемых Spring Data):

### [Именование Query-методов](https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html#jpa.query-methods.query-creation)

Пример:
```java
@Repository  
public interface PeopleJpaRepository extends JpaRepository<Person, Integer> {  
  
    List<Person> findByName(String name);  
}
```


---
## Тип возвращаемого значения

##### Если возвращает 1 или 0 элементов:

- `Optional<Person>`
- `Person`

Пример:
```java
@Query("SELECT u FROM User u WHERE u.login = :login AND u.hashedPassword = :hashedPassword")  
Optional<User> findByLoginAndHashedPassword(
	@Param("login") String login, 
	@Param("hashedPassword") String hashedPassword
);
```

#### Если возвращает более 1:

- `List<Person>`
- `Set<Person>`


#### Если возвращает 0 или более 1:

~={red}!!!!=~ Всё равно используем `List<>` ~={red}!!!!=~

`Optional<List<>>`~={red} - антипаттерн=~

---
### Поддерживает операцию 'не равен':

Spring Data JPA умеет парсить ключевые слова в имени метода и превращать их в JPQL. Для сравнения "не равно" используется суффикс `Not` или `IsNot`.

Примеры:
```java
List<RecordInfo> findByUserIdAndRecordStatusNot(UUID userId, RecordInfo.RecordStatus status);
```

или
```java
List<RecordInfo> findByUserIdAndRecordStatusIsNot(UUID userId, RecordInfo.RecordStatus
```


---
### Если самому надо прописать JPQL запрос:

> [!note]
> Для `UPDATE` / `DELETE` нужно использовать дополнительно @Modifying:
> 
> ```java
> @Modifying
> @Query("DELETE FROM LabelValue lv WHERE lv.recordInfo.recordId = :recordId")  
>void deleteByRecordId(@Param("recordId") UUID recordId);
> ```


```java
public interface LabelRepository extends JpaRepository<LabelValue, CalculationValue> {  
  
        @Query("SELECT lv FROM LabelValue lv " +  
                "WHERE lv.recordInfo.recordId = :recordId " +  
                "AND lv.gaapName = :gaapName")  
        List<LabelValue> findByRecordIdAndGaapName(@Param("recordId") UUID recordId,  
                                                   @Param("gaapName") String gaapName);  
}
```


Use case: 
Получение Entity по полю, по которому производится связь классов.
Пример такого Entity:
```java
  
@Entity  
@Table(name="label_value")  
public class LabelValue {  
  
    @Id  
    @OneToOne    @JoinColumn(name="calculation_id", referencedColumnName = "calculation_id")  
    private CalculationValue calculationValue;  
  
    @ManyToOne  
    @JoinColumn(name="record_id", referencedColumnName = "record_id")  
    private RecordInfo recordInfo;  
    @Column(name="\"value\"")  
    private String value;
    
    ...
```



----
#### [[Custom repository methods - Spring Data - Flashcards|Link to flashcards]]



---
### References:

- [[@Repository - Spring Data]]