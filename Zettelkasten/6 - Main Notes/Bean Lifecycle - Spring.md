
09-10-2025 20:33

Status: #baby 

Tags: [[Spring]]

---
# Bean Lifecycle - Spring


![[Pasted image 20260513172432.png|697]]

##### Создание бина

```
1. newInstance() ← конструктор
2. postProcessAfterInstantiation() ← InstantiationAwareBeanPostProcessor
3. populateBean() ← @Autowired, @Value ИНЪЕКТИТСЯ ЗДЕСЬ
4. postProcessBeforeInitialization() ← BeanPostProcessor
5. init() / @PostConstruct
6. postProcessAfterInitialization()
```

Подробнее:

**1. Инстанцирование объекта**. Техническое начало жизни бина, работа конструктора его класса;  
  
**3. Установка свойств** из конфигурации бина, внедренее зависимостей.
  
**4. Нотификация aware-интерфейсов**. `BeanNameAware`, `BeanFactoryAware` и другие. Технически, выполняется системными подтипами `BeanPostProcessor`, и совпадает с шагом 4.
  
**4. Пре-инициализация** – метод `postProcessBeforeInitialization()` интерфейса `BeanPostProcessor`;  
  
**5. Инициализация.** Разные способы применяются в таком порядке:  
**•** Метод бина с аннотацией `@PostConstruct` из стандарта JSR-250 (рекомендуемый способ);  
**•** Метод `afterPropertiesSet()` бина под интерфейсом `InitializingBean`;  
**•** Init-метод. Для отдельного бина его имя устанавливается в параметре определения `initMethod`. В xml-конфигурации можно установить для всех бинов сразу, с помощью `default-init-method`;  
  
**6. Пост-инициализация** – метод `postProcessAfterInitialization()` интерфейса `BeanPostProcessor`.
    

![[Pasted image 20260221191631.png]]

##### Уничтожение бина

**1.** Метод с аннотацией `@PreDestroy`;  
**2.** Метод с именем, которое указано в свойстве `destroyMethod` определния бина (или в глобальном `default-destroy-method`);  
**3.** Метод `destroy()` интерфейса `DisposableBean`.



## SmartLifecycle (дополнительно)

- Фазы: RAW → INITIALIZED → SMART → REFRESHED → STARTED/STOPPED.
    
- `DefaultLifecycleProcessor` управляет start/stop по приоритету фаз (PHASE_NORMAL).

----
#### [[Bean Lifecycle - Spring - Flashcards|Link to flashcards]]



---
### References:

