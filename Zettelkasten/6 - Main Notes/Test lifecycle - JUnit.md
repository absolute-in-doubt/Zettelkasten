
07-11-2025 12:32

Status: #baby 

Tags: [[Testing]] [[Java+]]

---
# Test lifecycle - JUnit

Для каждого тестового метода создаётся новая instance of the test class. 

Поэтому:

- `@BeforeEach` **и** `@AfterEach` могут быть **нестатическими** — они выполняются на каждом новом объекте.

- `@BeforeAll` **и** `@AfterAll` по умолчанию должны быть **static**


## Изменение ЖЦ тестов

Для изменения ЖЦ есть аннотация ~={purple}@TestInstance(`Lifestyle lyfestyle`)=~:

- Lifecycle.PER_METHOD - ~={red}**по умолчанию**=~. На каждый метод по новой instance of the test class. 

- Lifecycle.PER_CLASS - instance of the test class создаётся в единственном экземпляре.


----
#### [[Test lifecycle - JUnit - Flashcards|Link to flashcards]]



---
### References:

