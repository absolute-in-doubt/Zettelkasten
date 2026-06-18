
06-11-2025 21:08

Status:

Tags: [[Jakarta Validation]]

---
# Аннотации - Jakarta Validation

###### Booleans:

- ~={purple}@AssertFalse=~: Validates that the value must be false.

- ~={purple}@AssertTrue=~: Validates that the value must be true.


###### Numbers:

- ~={purple}@DecimalMax=~: Specifies that the field must be a number less than or equal to the specified maximum.

- ~={purple}@DecimalMin=~: Specifies that the field must be a number greater than or equal to the specified minimum.

- ~={purple}@Digits=~: Specifies the exact number of integral and fractional digits for a number.


- ~={purple}@Max=~: Validates that the value must be less than or equal to the specified maximum.

- ~={purple}@Min=~: Validates that the value must be greater than or equal to the specified minimum.

- ~={purple}@Negative=~: Validates that the value must be negative.

- ~={purple}@NegativeOrZero=~: Validates that the value must be negative or zero.


- ~={purple}@Positive=~: Validates that the value must be positive.

- ~={purple}@PositiveOrZero=~: Validates that the value must be positive or zero

###### Dates

- ~={purple}@Future=~: Specifies that the field must be a future date.

- ~={purple}@Past=~: Validates that the value must be in the past.

- ~={purple}@FutureOrPresent=~: Specifies that the field must be a future or present date.

- ~={purple}@PastOrPresent=~: Validates that the value must be in the past or present.


###### Text variables

- ~={purple}@Size=~: Validates that the value must be within the specified size range.

- ~={purple}@Pattern=~: Validates that the value matches a regular expression pattern.

- ~={purple}@NotBlank=~: Ensures that the value is not null, empty, or whitespace.

- ~={purple}@Email=~: Validates that the value is a valid email address.


###### Any field

- ~={purple}@NotEmpty=~: Ensures that the value is not null or empty.

- ~={purple}@NotNull=~: If applied to a field, asserts that the value must not be null.

- ~={purple}@Null=~: If applied to a field, asserts that the value must be null.


----
### Example:

```java
import javax.validation.constraints.*;  
  
public class Example {  
@AssertFalse  
private boolean isInvalid;  
@AssertTrue  
private boolean isValid;  
@DecimalMax(value = "1000.00")  
private double maxAmount;  
@DecimalMin(value = "10.50")  
private double minAmount;  
@Digits(integer = 5, fraction = 2)  
private BigDecimal preciseNumber;  
@Email  
private String emailField;  
@Future  
private Date futureDate;  
@FutureOrPresent  
private LocalDateTime futureOrPresentDateTime;  
@Max(100)  
private int maxLimit;  
@Min(18)  
private int minAge;  
@Negative  
private int negativeValue;  
@NegativeOrZero  
private int zeroOrNegativeValue;  
@NotBlank  
private String notBlankField;  
@NotEmpty  
private String notEmptyField;  
@NotNull  
private String notNullField;  
@Null  
private String nullField;  
@Past  
private Date pastDate;  
@PastOrPresent  
private LocalDateTime pastOrPresentDateTime;  
@Pattern(regexp = "\\d{3}-\\d{2}-\\d{4}")  
private String patternField;  
@Positive  
private int positiveValue;  
@PositiveOrZero  
private int zeroOrPositiveValue;  
@Size(min = 2, max = 50)  
private String sizedValue;  
}
```

----
#### [[Аннотации - Jakarta Validation - Flashcards|Link to flashcards]]



---
### References:

