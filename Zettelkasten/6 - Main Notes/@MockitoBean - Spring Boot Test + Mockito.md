
25-05-2026 20:48

Status:

Tags: [[Mockito]] [[Testing]] [[Java+]]

---
# @MockitoBean - Spring Boot Test + Mockito


В **Spring Boot 4.0.6** (и более новых версиях) появилась **новая аннотация** — **`@MockitoBean`** из пакета `org.springframework.test.context.bean.override.mockito`.


**`@MockitoBean`** — это **унифицированная аннотация** для создания **моков и шпионов** в **Spring-контексте**, которая **объединяет функциональность `@Mock` и `@MockBean`**. Она **автоматически определяет**, нужно ли создать **мок (`@Mock`)** или **подменить бин в контексте (`@MockBean`)**.

---

## Отличия от `@Mock` и `@MockBean`

@MockitoBean vs @Mock vs @MockBean

|Аннотация|Контекст|Переопределение бинов|Тип объекта|Пример|
|---|---|---|---|---|
|**`@Mock`**|❌ Вне Spring|❌ Нет|Мок|`@Mock private UserRepository repo;`|
|**`@MockBean`**|✅ В Spring|✅ Да|Мок|`@MockBean private UserRepository repo;`|
|**`@MockitoBean`**|✅ В Spring|✅ Да|**Мок или шпион** (автоматически)|`@MockitoBean private UserRepository repo;`|

---

## Как работает `@MockitoBean`?

1. **Если класс является Spring-бином** (например, `@Component`, `@Service`, `@Repository`), то `@MockitoBean` **подменяет его в контексте** (как `@MockBean`).
2. **Если класс не является Spring-бином**, то `@MockitoBean` **создаёт обычный мок** (как `@Mock`).
3. **Поддерживает шпионы** (если использовать с `@Spy` или `Mockito.spy()`).

---

## Примеры использования

### 1. Подмена Spring-бина (как `@MockBean`)

```java
@SpringBootTest
class UserServiceTest {

    @MockitoBean // Подменяет реальный UserRepository в контексте
    private UserRepository userRepository;

    @Autowired
    private UserService userService; // Получает мок-репозиторий

    @Test
    void testGetUser() {
        when(userRepository.findById(1L)).thenReturn(new User(1L, "Test"));
        User user = userService.getUser(1L);
        assertEquals("Test", user.getName());
    }
}
```

- **Что происходит:** `UserRepository` (если это Spring-бин) **подменяется моком** в контексте.

---

### **2. Создание мока для не-Spring класса (как `@Mock`)**


```java
@SpringBootTest
class PaymentServiceTest {

    @MockitoBean // Создаёт мок (если PaymentGateway не является Spring-бином)
    private PaymentGateway paymentGateway;

    @Autowired
    private PaymentService paymentService;

    @Test
    void testProcessPayment() {
        when(paymentGateway.charge(any())).thenReturn(true);
        boolean result = paymentService.processPayment(100);
        assertTrue(result);
    }
}
```

- **Что происходит:** Если `PaymentGateway` **не является Spring-бином**, то `@MockitoBean` работает как `@Mock`.

---

### **3. Использование с шпионами**

```java
@SpringBootTest
class UserServiceTest {

    @MockitoBean(spy = true) // Создаёт шпион вместо мока
    private UserRepository userRepository;

    @Test
    void testSpyBehavior() {
        User realUser = new User(1L, "Real");
        when(userRepository.findById(1L)).thenReturn(realUser);

        User user = userRepository.findById(1L);
        assertEquals("Real", user.getName());

        // Проверяем, что метод был вызван
        verify(userRepository, times(1)).findById(1L);
    }
}
```

- **`spy = true`** — создаёт **шпион** вместо мока.

---

## Почему ввели `@MockitoBean`?

1. **Унификация:**
    - Объединяет `@Mock` и `@MockBean` в одну аннотацию.
2. **Автоматическое определение контекста:**
    - Сама определяет, нужно ли подменять бин в Spring-контексте или создать обычный мок.
3. **Поддержка шпионов:**
    - Позволяет легко создавать шпионы через `spy = true`.
4. **Совместимость с новыми версиями Spring:**
    - Работает с **Spring Framework 6.0+** и **Spring Boot 3.0+**.

---

## **Итоги**

|Вопрос|Ответ|
|---|---|
|**Что делает `@MockitoBean`?**|Создаёт мок или шпион, автоматически подменяя бины в Spring-контексте.|
|**Чем отличается от `@Mock`?**|`@MockitoBean` работает в Spring-контексте и может подменять бины.|
|**Чем отличается от `@MockBean`?**|`@MockitoBean` универсальнее: может работать как `@Mock` (для не-Spring классов) и как `@MockBean` (для Spring-бинов).|
|**Когда использовать `@MockitoBean`?**|В **Spring Boot 4.0.6+** для упрощения тестирования (вместо `@Mock` + `@MockBean`).|
|**Как создать шпион с `@MockitoBean`?**|`@MockitoBean(spy = true)`.|

----
#### [[@MockitoBean - Spring Boot Test + Mockito - Flashcards|Link to flashcards]]



---
### References:

