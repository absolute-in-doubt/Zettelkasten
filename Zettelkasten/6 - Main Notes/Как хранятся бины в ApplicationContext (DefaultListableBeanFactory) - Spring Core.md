
20-06-2026 10:54

Status:

Tags: [[Spring Core]] [[Spring]]

---
# Как хранятся бины в ApplicationContext (DefaultListableBeanFactory) - Spring Core


```java
public class DefaultListableBeanFactory extends AbstractAutowireCapableBeanFactory implements ConfigurableListableBeanFactory, BeanDefinitionRegistry, Serializable {  

    public static final String STRICT_LOCKING_PROPERTY_NAME = 
			    "spring.locking.strict";  
    private static @Nullable Class<?> jakartaInjectProviderClass;  
    private static final Map<String, Reference<DefaultListableBeanFactory>> 
			    serializableFactories;  
    private final @Nullable Boolean strictLocking = 
			    SpringProperties.checkFlag("spring.locking.strict");  
    private @Nullable String serializationId;  
    private @Nullable Boolean allowBeanDefinitionOverriding;  
    private boolean allowEagerClassLoading = true;  
    private @Nullable Executor bootstrapExecutor;  
    private @Nullable Comparator<Object> dependencyComparator;  
    private AutowireCandidateResolver autowireCandidateResolver;  
    
    private final Map<Class<?>, Object> resolvableDependencies;  
    
    private final Map<String, BeanDefinition> beanDefinitionMap;  
    private final Map<String, BeanDefinitionHolder> 
			    mergedBeanDefinitionHolders;  
    private final Map<String, Class<?>> primaryBeanNamesWithType;  
    private final Map<Class<?>, String[]> allBeanNamesByType;  
    private final Map<Class<?>, String[]> singletonBeanNamesByType;  
    
    private volatile List<String> beanDefinitionNames;  
    private volatile Set<String> manualSingletonNames;  
    private volatile String @Nullable [] frozenBeanDefinitionNames;  
    private volatile boolean configurationFrozen;  
    private volatile @Nullable String mainThreadPrefix;  
    private final NamedThreadLocal<PreInstantiation> preInstantiationThread;
    
    
    
    public DefaultListableBeanFactory() {  
    this.autowireCandidateResolver = SimpleAutowireCandidateResolver.INSTANCE;  
    this.resolvableDependencies = new ConcurrentHashMap(16);  
    this.beanDefinitionMap = new ConcurrentHashMap(256);  
    this.mergedBeanDefinitionHolders = new ConcurrentHashMap(256);  
    this.primaryBeanNamesWithType = new ConcurrentHashMap(16);  
    this.allBeanNamesByType = new ConcurrentHashMap(64);  
    this.singletonBeanNamesByType = new ConcurrentHashMap(64);  
    this.beanDefinitionNames = new ArrayList(256);  
    this.manualSingletonNames = new LinkedHashSet(16);  
    this.preInstantiationThread = new NamedThreadLocal("Pre-instantiation thread marker");  
}
    
    ...
    
    }
```



#### Плюс `DefaultListableBeanFactory` расширяет `DefaultSingletonBeanRegistry`:

```java
public class DefaultSingletonBeanRegistry implements SingletonBeanRegistry, DisposableBeanRegistry {
    
    // КАРТА 1: Полностью созданные singleton бины (ready beans)
    private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);
    
    // КАРТА 2: Ранние ссылки (early references) — бины в процессе создания (half-initialized)
    private final Map<String, Object> earlySingletonObjects = new ConcurrentHashMap<>(16);
    
    // КАРТА 3: ObjectFactory для создания бинов (для разрешения циклических зависимостей)
    private final Map<String, ObjectFactory<?>> singletonFactories = new ConcurrentHashMap<>(16);
    
    // НАБОР: Бины, которые сейчас в процессе создания
    private final Set<String> singletonsCurrentlyInCreation = 
        new ConcurrentHashMapKeyTypeSet<>(this.singletonObjects);
}
```




| Map                             | Где находится                  | Что хранит                             |
| ------------------------------- | ------------------------------ | -------------------------------------- |
| `singletonObjects`              | `DefaultSingletonBeanRegistry` | Готовые singleton **объекты**          |
| `earlySingletonObjects`         | `DefaultSingletonBeanRegistry` | уже полученная ранняя ссылка           |
| `singletonFactories`            | `DefaultSingletonBeanRegistry` | способ получить раннюю ссылку (лениво) |
| `singletonsCurrentlyInCreation` | `DefaultSingletonBeanRegistry` | Названия бинов в процессе создания     |
| `beanDefinitionMap`             | `DefaultListableBeanFactory`   | BeanDefinition **метадата** всех бинов |
| `resolvableDependencies`        | `DefaultListableBeanFactory`   | Зависимости по типу (не бины)          |
| `allBeanNamesByType`            | `DefaultListableBeanFactory`   | Cache: все бины по типу                |
| `singletonBeanNamesByType`      | `DefaultListableBeanFactory`   | Cache: singleton бины по типу          |


---

## Самые важные Map для инициализации бинов


### Priority 1: Критические для создания бинов

| Карта                       | Где                            | Для чего                                                                    |
| --------------------------- | ------------------------------ | --------------------------------------------------------------------------- |
| **`beanDefinitionMap`**     | `DefaultListableBeanFactory`   | **Главная карта** — хранит ВСЕ BeanDefinition (метадата для создания бинов) |
| **`singletonObjects`**      | `DefaultSingletonBeanRegistry` | **Готовые бины** — куда складываются полностью созданные singleton бины     |
| **`earlySingletonObjects`** | `DefaultSingletonBeanRegistry` | уже полученная ранняя ссылка                                                |
| **`singletonFactories`**    | `DefaultSingletonBeanRegistry` | способ получить раннюю ссылку (лениво)                                      |

### Priority 2: Кэши (не критичны, но ускоряют)

| Карта                      | Где                          | Для чего                          |
| -------------------------- | ---------------------------- | --------------------------------- |
| `allBeanNamesByType`       | `DefaultListableBeanFactory` | Cache для `getBeansOfType()`      |
| `singletonBeanNamesByType` | `DefaultListableBeanFactory` | Cache для singleton бинов по типу |

### Priority 3: Метадата (не для создания, но для настройки)

| Карта                    | Где                          | Для чего                                              |
| ------------------------ | ---------------------------- | ----------------------------------------------------- |
| `resolvableDependencies` | `DefaultListableBeanFactory` | Зависимости по типу (ApplicationContext, BeanFactory) |
| `mergedBeanDefinitions`  | `DefaultListableBeanFactory` | Child BeanDefinition (наследование)                   |

---

## Полный алгоритм создания бина с BFPP и BPP

## Этап 1: Парсинг конфигурации → BeanDefinition

```
┌─────────────────────────────────────────────────────────┐
│ ЭТАП 1: Парсинг (@Component, @Bean, XML)                │
└─────────────────────────────────────────────────────────┘

1. XmlBeanDefinitionParser / AnnotationBeanReader
   ↓
2. Создаёт RootBeanDefinition / ChildBeanDefinition
   ↓
3. beanDefinitionMap.put("userService", beanDefinition)
   │
   └─> DefaultListableBeanFactory.beanDefinitionMap
       ["userService" → BeanDefinition(class=UserService, props={...})]
```

**BFPP здесь НЕ применяется** — это этап регистрации метадата.

---

### Этап 2: BeanFactoryPostProcessor (БЕЗ создания бинов)


```
┌─────────────────────────────────────────────────────────┐
│ ЭТАП 2: BeanFactoryPostProcessor                        │
└─────────────────────────────────────────────────────────┘

4. getBeanPostProcessors() → BFPP список
   ↓
5. BFPP.postProcessBeanFactory(beanFactory)
   │
   ├─> Пример: @Configuration ClassPostProcessor
   ├─> Пример: PropertyPlaceholderConfigurer
   ├─> Пример: CustomScopeConfigurer
   │
   └─> MODIFIES beanDefinitionMap (не создаёт бины!)
       beanDefinitionMap["userService"].setProperty("dbName", "prod_db")
```

**Важно:** BFPP работает **ДО создания любого бина** (кроме самих BFPP).

**Что происходит в картах:**

- `beanDefinitionMap` — модифицируется (метадата изменена)
    
- `singletonObjects` — пусто (бины еще не созданы)
    
- `earlySingletonObjects` — пусто
    
- `singletonFactories` — пусто
    

---

## Этап 3: Создание BPP бинов (особый случай)

```
┌─────────────────────────────────────────────────────────┐
│ ЭТАП 3: Создание BeanPostProcessor бинов                │
│ (BPP нужно создать BEFORE остальных бинов)              │
└─────────────────────────────────────────────────────────┘

6. getBeanPostProcessors() → ищет бины с @BeanPostProcessor
   ↓
7. createBean("requestScope") → BPP бин
   │
   ├─ new Object() → raw ( userService = new UserService() )
   │
   ├─ populateBean(userService) → @Autowired, @Value
   │
   ├─ BPP.postProcessBeforeInitialization(userService, "userService")
   │  │
   │  └─> Можно добавить моки, настроить свойства
   │
   ├─ initializeBean(userService)
   │  ├─ @PostConstruct
   │  ├─ afterPropertiesSet()
   │  ├─ init-method()
   │
   ├─ BPP.postProcessAfterInitialization(userService, "userService")
   │  │
   │  └─> AOP proxy (если нужно)
   │
   └─> singletonObjects.put("userService", userService)
       │
       └─> DefaultSingletonBeanRegistry.singletonObjects
```

**Что происходит в картах:**

- `beanDefinitionMap` — уже содержит BeanDefinition для BPP
    
- `singletonObjects` — **записывается** готовый BPP бин
    
- `earlySingletonObjects` — пусто
    
- `singletonFactories` — пусто
    

---

### Этап 4: Создание обычного бина (main flow)


```
┌─────────────────────────────────────────────────────────┐
│ ЭТАП 4: Создание обычного бина (например, "userService")│
└─────────────────────────────────────────────────────────┘

8. getBean("userService")
   ↓
9. doGetBean("userService")
   │
   ├─⊗ getSingleton("userService")
   │   └─> singletonObjects.get("userService") → null (нет)
   │
   ├─⊗ isSingletonCurrentlyInCreation("userService")
   │   └─> false (не в creation)
   │
   └─> createBean("userService")
       │
       ├─ step 1: new Object() → raw instance
       │   userService = new UserService()
       │
       ├─ step 2: populateBean(userService)
       │   ├─ resolveDependencies() → @Autowired
       │   │   ├─ getBean("userRepository")
       │   │   │   └─> (рекурсивно создаёт зависимости)
       │   │   │
       │   │   └─ resolveResolvableDependency(UserRepository.class)
       │   │       └─> resolvableDependencies.get(UserRepository.class)
       │   │
       │   └─ applyPropertyValues() → @Value, setXxx()
       │
       ├─ step 3: initializeBean(userService)
       │   │
       │   ├─⊗ BPP.postProcessBeforeInitialization(userService, "userService")
       │   │   │
       │   │   ├─> CommonAnnotationBeanPostProcessor
       │   │   ├─> AutowiredAnnotationBeanPostProcessor
       │   │   │
       │   │   └─> Модификует объект (добавляет моки, данные)
       │   │
       │   ├─⊗ invokeInitMethods(userService)
       │   │   ├─> @PostConstruct → init()
       │   │   ├─> afterPropertiesSet() (если InitializableBean)
       │   │   ├─> init-method() (если init-method="init")
       │   │
       │   └─⊗ BPP.postProcessAfterInitialization(userService, "userService")
       │       │
       │       ├─> AnnotationAwareAnnotatedBeanPostProcessor
       │       ├─> CustomAOPAdvisor
       │       │
       │       └─> Возвращает wrapped объект (AOP proxy)
       │
       └─ step 4: addSingleton("userService", finalBean)
           │
           └─> singletonObjects.put("userService", finalBean)
               │
               └─> DefaultSingletonBeanRegistry.singletonObjects
                   ["userService" → UserService_с_AOP_proxy]
```

## Что происходит в картах по шагам:

| Шаг              | Карта                           | Действие                                     |
| ---------------- | ------------------------------- | -------------------------------------------- |
| **start**        | `beanDefinitionMap`             | Уже содержит `BeanDefinition("userService")` |
| **start**        | `singletonObjects`              | Пусто                                        |
| **new Object()** | `singletonsCurrentlyInCreation` | **add** → `["userService"]`                  |
| **populateBean** | `resolvableDependencies`        | **read** → `@Autowired UserRepository`       |
| **initialize**   | `earlySingletonObjects`         | Пусто (если не цикличная зависимость)        |
| **BPP-before**   | `singletonObjects`              | Пусто (бин еще не готов)                     |
| **BPP-after**    | `singletonObjects`              | Пусто (бин до записи)                        |
| **addSingleton** | `singletonObjects`              | **write** → `["userService" → готовый бин]`  |
| **addSingleton** | `singletonsCurrentlyInCreation` | **remove** → `[]`                            |

---

## Этап 5: Цикличная зависимость (special case)

[[Circular dependencies - Spring Core]]

```
┌─────────────────────────────────────────────────────────┐
│ Цикличная зависимость: ServiceA ↔ ServiceB              │
│ - ServiceA зависит от ServiceB                          │
│ - ServiceB зависит от ServiceA                          │
└─────────────────────────────────────────────────────────┘

createBean("serviceA")
  ├─ 1. beforeCreateBean()
  │   ├─ registerSingletonFactory("serviceA",() -> createBeanObject("serviceA"))
  │   │   │
  │   │   └─ singletonFactories.put("serviceA", ObjectFactory)
  │   │       ["serviceA" → () -> new ServiceA()]
  │   │
  │   └─ singletonsCurrentlyInCreation.add("serviceA")
  │       ["serviceA"]
  │
  ├─ 2. createBeanObject() → rawA = new ServiceA()
  │
  ├─ 3. populateBean(rawA) → @Autowired ServiceB
  │   └─ getBean("serviceB")
  │       ├─ createBean("serviceB")
  │       │   ├─ registerSingletonFactory("serviceB", ...)
  │       │   ├─ rawB = new ServiceB()
  │       │   └─ populateBean(rawB) → @Autowired ServiceA
  │       │       ├─ getBean("serviceA")
  │       │       │   ├─ isSingletonCurrentlyInCreation("serviceA") → true!
  │       │       │   ├─ singletonFactories.get("serviceA")
  │       │       │   │   └─> ObjectFactory → создает early reference
  │       │       │   │
  │       │       │   ├─> earlySingletonObjects.put("serviceA", rawA)
  │       │       │   │
  │       │       │   └─> Возвращает rawA
  │       │       │
  │       │       └─ rawB.serviceA = rawA
  │       │
  │       └─ rawB → B_final → addSingleton("serviceB", B_final)
  │
  ├─ 4. rawA.serviceB = B_final
  │
  └─ 5. rawA → A_final → addSingleton("serviceA", A_final)
      ├─ singletonObjects.put("serviceA", A_final)
      ├─ singletonFactories.remove("serviceA")  ← ОЧИЩАЕТСЯ
      └─ singletonsCurrentlyInCreation.remove("serviceA")
```

---

## Сводная таблица: когда карты заполняются

|Карта|Заполняется|Удаляется|
|---|---|---|
|`beanDefinitionMap`|**Этап 1** (парсинг)|При `context.close()`|
|`singletonObjects`|**Этап 4** (after BPP-after)|При `context.close()`|
|`earlySingletonObjects`|**Этап 5** (цикличная dep)|Сразу после использования|
|`singletonFactories`|**Этап 4** (before create)|После создания бина|
|`singletonsCurrentlyInCreation`|**Этап 4** (start create)|**Этап 4** (after addSingleton)|
|`allBeanNamesByType`|**Lazy** (первый `getBeansOfType`)|При `context.close()`|
|`resolvableDependencies`|**Этап 0** (prepareBeanFactory)|При `context.close()`|

---

## Визуализация жизненного цикла бина:

```
┌────────────────────────────────────────────────────────────────┐
│ BeanDefinition (метадата)                                      │
│  ↓ (Этап 1)                                                    │
│ beanDefinitionMap.put("userService", BeanDefinition)           │
└────────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────────┐
│ BFPP (before создания бинов)                                   │
│  ↓ (Этап 2)                                                    │
│ beanDefinitionMap модифицируется (метадата изменена)           │
└────────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────────┐
│ createBean("userService")                                      │
│  ↓                                                             │
│ 1. new UserService() → raw                                     │
│ 2. singletonsCurrentlyInCreation.add("userService")            │
│ 3. populateBean() → @Autowired (resolvableDependencies)        │
│ 4. BPP.postProcessBeforeInitialization()                       │
│ 5. @PostConstruct / afterPropertiesSet()                       │
│ 6. BPP.postProcessAfterInitialization() → wrapped (AOP)        │
│ 7. singletonObjects.put("userService", finalBean)              │
│ 8. singletonsCurrentlyInCreation.remove("userService")         │
└────────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────────┐
│ Готовый бин                                                    │
│  ↓                                                             │
│ singletonObjects["userService"] → Ваш готовый UserService      │
└────────────────────────────────────────────────────────────────┘
```


----
#### [[Как хранятся бины в ApplicationContext (DefaultListableBeanFactory) - Spring Core - Flashcards|Link to flashcards]]



---
### References:

