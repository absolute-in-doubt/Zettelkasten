
07-11-2025 12:22

Status: #baby 

Tags: [[Testing]] [[Java+]]

---
# Helpful annotations - JUnit


### Нейминг тестов

~={purple}@DisplayName(`"Имя теста любыми Unicode символами"`)=~

~={purple}@DisplayNameGeneration(`DisplayNameGenerator generator`)=~
```java
@DisplayNameGeneration(DisplayNameGenerator.ReplaceUnderscores.class)
void test_if_value_is_negative(){...}
```


----
#### [[Helpful annotations - JUnit - Flashcards|Link to flashcards]]



---
### References:

