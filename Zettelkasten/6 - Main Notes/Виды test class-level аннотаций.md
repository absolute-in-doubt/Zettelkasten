
03-03-2026 17:01

Status:

Tags:

---
# Виды test class-level аннотаций


## 1. `@DataJpaTest`

Эта аннотация  **поднимает только часть контекста**, ориентированную на JPA‑репозитории.

- По умолчанию:
    
    - включает `@AutoConfigureTestDatabase` (обычно встроенный H2‑экземпляр);
        
    - создаёт только `EntityManager`, `JPA` и `Spring Data JPA`‑репозитории;
        
    - **не поднимает** контроллеры, веб‑часть, полноценный `@SpringBootApplication`‑контекст.
        
- В контекст **обычно попадают**:
    
    - ваши репозитории (`@Repository`, интерфейсы `JpaRepository`);
        
    - связанные с ними JPA‑сущности;
        
    - иногда очень ограниченный набор пользовательских сервис‑бинов, если они явно добавлены через `@Import` или `@TestConfiguration`.

---

## 2. `@ContextConfiguration`

Основная аннотация для загрузки `ApplicationContext` на уровне класса.

- Указывает **конкретные классы конфигурации** (`@Configuration`, `@Component`, `@Service` и т.п.) через атрибут `classes` → загружается только контекст, построенный из этих классов.  
    Пример: если передать `@ContextConfiguration(classes = TestConfig.class)`, Spring:
    
    - сканирует **только `TestConfig`** и явно объявленные в нём бины;
        
    - **не сканирует** пакеты приложения по умолчанию, если это не задано внутри `TestConfig` (через `@ComponentScan`).spring+1
        
- Альтернативно можно указывать **XML/Groovy‑ресурсы** через `locations` → контекст строится только из этих файлов, без Java‑компонента‑сканирования по умолчанию.docs.spring+1
    
- Если `@ContextConfiguration` не задан на тест‑классе, Spring **не создаёт контекст** (если не используется `@SpringBootTest` или `@WebApplicationContext`‑аннотации).spring+1
    

---

## 2. `@SpringBootTest`

Специфичная для Spring Boot аннотация, поднимает **«почти весь» application context** (интеграционный тест по умолчанию).

- По умолчанию:
    
    - `@SpringBootTest` ищет класс с `@SpringBootApplication` / `@SpringBootConfiguration` (верх‑уровневый класс‑точка входа) и **сканирует пакет и его подпакеты**;
        
    - в контекст загружается:
        
        - все компоненты (`@Component`, `@Service`, `@Repository`, `@Controller` и т.д.);
            
        - `@Configuration`‑классы;
            
        - авто‑конфигурации (`spring-boot-autoconfigure`), если они не отключены явно.stackoverflow+1
            
- Если указать `@SpringBootTest(classes = CustomConfig.class)`:
    
    - Spring **не сканирует** пакеты приложения;
        
    - контекст строится **только из `CustomConfig`** и бинов, на которые ссылается эта конфигурация (включая импортированные `@Import` и др.).docs.spring+1
        
- Атрибут `webEnvironment` влияет на **тип контекста**:
    
    - `MOCK` / `RANDOM_PORT` — поднимает `WebApplicationContext` (если `@SpringBootTest` используется без `webEnvironment`/по умолчанию — всё равно не обязательно `WebApplicationContext`, зависит от других аннотаций).​
        

---

## 3. `@WebAppConfiguration`

Аннотация из Spring Framework, указывает, что нужен **веб‑контекст (`WebApplicationContext`)**.

- В комбинации `@ContextConfiguration` + `@WebAppConfiguration`:
    
    - создаётся **`WebApplicationContext`** поверх `WebEnvironment`‑ориентированного контекста;
        
    - загружается **только то, что объявлено** в `@ContextConfiguration` (например, XML‑конфигурация или Java‑классы), но уже в контексте `WebApplicationContext` с `MockServletContext`.
        
- Без `@ContextConfiguration` `@WebAppConfiguration` по‑умолчанию не создаёт контекст; нужна связка с `@ContextConfiguration` или `@SpringBootTest`.​
    

---

## 4. `@ContextHierarchy`

Аннотация для **иерархии контекстов** (parent/child).

- `@ContextHierarchy` определяет несколько уровней `@ContextConfiguration` (например, родительский контекст + дочерний‑веб‑контекст).
    
- Каждый уровень:
    
    - строит свой контекст из указанных `locations`/`classes`;
        
    - наследует бины из родительских уровней, но **не сканирует новые пакеты**, если не задано явно в `@ComponentScan` внутри конфиг‑класса.docs.spring.vmware+1
        

---

## 5. `@ActiveProfiles` и `@TestPropertySource`

- `@ActiveProfiles` указывает, какие **профили** активны при загрузке контекста:
    
    - Spring загружает только те бины/конфиги, которые **активны в указанных профилях**;
        
    - прочие бины (с другими профилями) не создаются.​
        
- `@TestPropertySource` (на уровне класса) добавляет **тестовые свойства** в `Environment`:
    
    - эти свойства доступны при построении контекста (а значит, могут влиять на создание бинов, например, через `@ConditionalOnProperty`), но **не добавляют новые компоненты**, только корректируют существующие.


---

## Краткая сводка по «что загружается»

| Аннотация                        | Какие части контекста загружаются / ограничиваются                                                   |
| -------------------------------- | ---------------------------------------------------------------------------------------------------- |
| `@ContextConfiguration`          | Только указанные `locations` или `classes` (и импорты/сканы внутри них).                             |
| `@SpringBootTest` (по умолчанию) | Почти весь application‑контекст, начиная с `@SpringBootApplication` и его пакета.                    |
| `@SpringBootTest(classes = …)`   | Только контекст из явно указанных классов, без автоматического сканирования.                         |
| `@WebAppConfiguration`           | То же, что в `@ContextConfiguration`, но как `WebApplicationContext`.                                |
| `@ContextHierarchy`              | Несколько уровней контекстов, каждый из своих `locations`/`classes`, с наследованием бинов. spring+1 |
| `@ActiveProfiles`                | Только бины, активные в указанных профилях.                                                          |
| `@TestPropertySource`            | Только дополнительные свойства; не добавляет новые компоненты.                                       |


----
#### [[Виды test class-level аннотаций - Flashcards|Link to flashcards]]



---
### References:

