
09-10-2025 17:44

Status: #not_even_born 

Tags: [[Patterns]]

---
# DI Container


**DI-контейнер** — это **частный случай [[IoC Container|IoC-контейнера]]**, который сфокусирован именно на:

- **внедрении зависимостей** в объекты (через конструктор, поля или сеттеры),
    
- разрешении зависимостей (Dependency Resolution),
    
- управлении графом зависимостей.
    

📌 Пример:  
У тебя есть `MyService` и `MyRepository`.  
Spring **внедрит `MyRepository` в `MyService`** без явного создания объектов вручную:

```java
@Service
public class MyService {
    private final MyRepository repo;

    public MyService(MyRepository repo) {
        this.repo = repo;
    }
}
```





| Характеристика    | [[IoC Container\|IoC Container]] 🧭              | DI Container 🧰                            |
| ----------------- | ------------------------------------------------ | ------------------------------------------ |
| Уровень           | Концептуально шире                               | Частный случай IoC                         |
| Главная задача    | Управление созданием и жизненным циклом объектов | Внедрение зависимостей между объектами     |
| Пример            | ApplicationContext                               | Механизм автосвязывания через `@Autowired` |
| Обязательно ли DI | Нет                                              | Да                                         |
| Отношение         | IoC включает в себя DI                           | DI является одной из реализаций IoC        |

----
#### [[DI Container - Flashcards|Link to flashcards]]



---
### References:

- [[Dependency Injection]]