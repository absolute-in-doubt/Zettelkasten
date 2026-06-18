
25-05-2026 19:03

Status:

Tags: [[Mockito]] [[Testing]] [[Java+]]

---
# Основные аннотации - Mockito


Аннотации Mockito

| Аннотация                                                                                     | Назначение                                                | Пример                                                |
| --------------------------------------------------------------------------------------------- | --------------------------------------------------------- | ----------------------------------------------------- |
| **[[Создание mock, @Mock + @InjectMocks - Mockito#Инъекция mock-ов через аннотацию\|@Mock]]** | Создаёт **мок-объект** (заглушку)                         | `@Mock private UserService userService;`              |
| **`@Spy`**                                                                                    | Создаёт **шпиона** (реальный объект с подменой методов)   | `@Spy private UserService userService;`               |
| **`@InjectMocks`**                                                                            | Внедряет моки/шпионов в тестируемый объект                | `@InjectMocks private UserController userController;` |
| **`@Captor`**                                                                                 | Создаёт **ArgumentCaptor** для захвата аргументов         | `@Captor private ArgumentCaptor captor;`              |
| **[[@MockBean - Spring Boot Test + Mockito\|@MockBean]]**                                     | Создаёт мок-бин в **Spring-контексте** (Spring Boot Test) | `@MockBean private UserRepository userRepository;`    |
| **`@SpyBean`**                                                                                | Создаёт шпион-бин в **Spring-контексте**                  | `@SpyBean private UserService userService;`           |

---

## **📌 Ключевые объекты и классы Mockito**

Ключевые объекты Mockito

|Объект/Класс|Назначение|Пример|
|---|---|---|
|**`Mockito.mock()`**|Создание мока вручную|`UserService mock = Mockito.mock(UserService.class);`|
|**`Mockito.spy()`**|Создание шпиона вручную|`UserService spy = Mockito.spy(userService);`|
|**`Mockito.when()`**|Задание поведения мока|`when(mock.getName()).thenReturn("Test");`|
|**`Mockito.doThrow()`**|Задание исключения|`doThrow(new RuntimeException()).when(mock).save();`|
|**`Mockito.verify()`**|Проверка вызовов|`verify(mock, times(1)).getName();`|
|**`ArgumentCaptor`**|Захват аргументов|`verify(mock).save(captor.capture());`|
|**`Mockito.any()`**|Любой аргумент|`when(mock.save(any(User.class))).thenReturn(true);`|
|**`Mockito.eq()`**|Точное совпадение аргумента|`when(mock.save(eq("admin"))).thenReturn(true);`|
|**`Mockito.times()`**|Указание количества вызовов|`verify(mock, times(2)).getName();`|
|**`Mockito.never()`**|Проверка, что метод **не вызывался**|`verify(mock, never()).delete();`|
|**`Mockito.anyInt()`**|Любой `int`|`when(mock.getById(anyInt())).thenReturn(user);`|
|**`Mockito.anyString()`**|Любая строка|`when(mock.findByName(anyString())).thenReturn(user);`|

---

## **📌 Ключевые методы Mockito**

Ключевые методы Mockito

|Метод|Назначение|
|---|---|
|**`when(...).thenReturn(...)`**|Задаёт возвращаемое значение|
|**`when(...).thenThrow(...)`**|Задаёт бросаемое исключение|
|**`doReturn(...).when(...)`**|Альтернатива `when` для `void` методов|
|**`doThrow(...).when(...)`**|Бросает исключение для `void` методов|
|**`verify(...).method(...)`**|Проверяет, что метод был вызван|
|**`verify(..., times(n)).method(...)`**|Проверяет количество вызовов|
|**`verify(..., never()).method(...)`**|Проверяет, что метод **не вызывался**|
|**`reset(mock)`**|Сбрасывает состояние мока|
|**`clearInvocations(mock)`**|Очищает историю вызовов мока|

---

## Ключевые концепции

- **Мок (Mock)** — заглушка, полностью подменяет реальный объект.
- **Шпион (Spy)** — реальный объект, но с возможностью подмены отдельных методов.
- **Внедрение моков (`@InjectMocks`)** — автоматически внедряет моки/шпионы в тестируемый класс.
- **Захват аргументов (`ArgumentCaptor`)** — позволяет проверить, с какими аргументами вызывался метод.
- **Проверка вызовов (`verify`)** — проверяет, был ли вызван метод и сколько раз.

---

## Минимальная конфигурация для работы

- **`@ExtendWith(MockitoExtension.class)`** — включает поддержку аннотаций Mockito в JUnit 5.
- Для **Spring Boot** используйте `@SpringBootTest` + `@MockBean`.

```java
@ExtendWith(MockitoExtension.class) // Для JUnit 5
public class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    public void testGetUserById() {
        when(userRepository.findById(1L)).thenReturn(new User(1L, "Test"));
        User user = userService.getUserById(1L);
        assertEquals("Test", user.getName());
    }
}
```

----
#### [[Основные аннотации - Mockito - Flashcards|Link to flashcards]]



---
### References:

