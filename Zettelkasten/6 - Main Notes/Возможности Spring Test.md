
07-01-2026 11:40

Status:

Tags: [[Spring]]

---
# Возможности Spring Test


`spring-test` — это модуль, который добавляет к Spring набор инструментов для модульного и интеграционного тестирования: управление `ApplicationContext` в тестах, DI в тесты, утилиты для веб‑тестов, мок‑объекты и т.п.

---

## 1. Spring TestContext Framework

Основная часть модуля — инфраструктура для запуска тестов с контекстом Spring. 

- Управление жизненным циклом `ApplicationContext` и кэширование контекста между тестами.  
- Интеграция с JUnit 4, JUnit 5, TestNG через расширения/раннеры:
  - JUnit 5: `SpringExtension`, аннотации ~={purple}@SpringJUnitConfig=~, ~={purple}@SpringJUnitWebConfig=~. 
  - JUnit 4: `SpringJUnit4ClassRunner`, ~={purple}@RunWith(`SpringJUnit4ClassRunner.class`)=~  
  
- Автоматическая инициализация контекста на основе:
  - Java‑конфигурации (`@Configuration` классы);
  - XML‑конфигов;
  - смешанных вариантов. 

**Ключевые аннотации:**

- **~={purple}@ContextConfiguration=~** — откуда брать конфигурацию (классы, XML, начальные параметры). 
- **~={purple}@ActiveProfiles=~** — какие Spring‑профили активировать в тесте. 
- **~={purple}@DirtiesContext=~** — пометить контекст «грязным», чтобы не кэшировать (пересоздать после теста). 
- ~={purple}**@TestPropertySource**=~ — добавить/переопределить свойства (аналог отдельного `*.properties` для теста).

---

## 2. Dependency Injection в тестовые классы

Spring может инжектить бины из `ApplicationContext` прямо в поля / конструктор теста. 

- Поддерживаются `@Autowired`, `@Qualifier`, `@Value` и другие стандартные механизмы DI.  
- Можно инжектить:
  - любые бины: сервисы, DAO, `DataSource`, `Environment`, `ApplicationContext` и т.д.;  
  - тестовые компоненты, определённые специально для тестов.

Пример (JUnit 5):

```java
@SpringJUnitConfig(SpringConfig.class)
class UserServiceTest {

    @Autowired
    private UserService userService;

    @Test
    void testFindAll() {
        var users = userService.findAll();
        // assertions...
    }
}
```

---
### References:

