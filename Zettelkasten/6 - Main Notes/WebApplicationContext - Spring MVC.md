
07-01-2026 12:04

Status:

Tags: [[Spring]]

---
# WebApplicationContext - Spring MVC

В Spring есть базовый **`ApplicationContext`** и его веб‑расширение **`WebApplicationContext`**. В веб‑приложениях Spring MVC обычно используется **иерархия контекстов**: корневой (root) `ApplicationContext` и один или несколько дочерних `WebApplicationContext` для DispatcherServlet’ов.

## Два типа контекста в веб‑приложении

В классической конфигурации Spring MVC:

- **Root ApplicationContext**
    
    - Общий для всего приложения.
    
    - Хранит «инфраструктурные» и бизнес‑бины: сервисы, репозитории, DataSource, транзакционный менеджер и т.п.
    
    - Создаётся слушателем (раньше `ContextLoaderListener`), живёт на уровне всего веб‑приложения.
    
- **WebApplicationContext**
    
    - Наследует бины из root‑контекста и добавляет «веб‑слой»: контроллеры, ViewResolver, HandlerMapping, конвертеры и др.
        
    - Как правило, **по одному `WebApplicationContext` на каждый `DispatcherServlet`**.
        
    - Может переопределять бины из родительского контекста и добавлять свои.
	
	- ~={orange}`Filters`, перед регистрацией в `FilterChain` хранятся именно здесь=~

Иерархия примерно такая:

```text
Servlet container (Tomcat)
 └─ Root ApplicationContext (Spring)
     ├─ WebApplicationContext для DispatcherServlet #1
     └─ WebApplicationContext для DispatcherServlet #2 (опционально)
```

## Что такое WebApplicationContext

`WebApplicationContext` — это интерфейс, который **расширяет `ApplicationContext` и «знает» про веб‑окружение**:

```java
public interface WebApplicationContext extends ApplicationContext {
    ServletContext getServletContext();
}
```

> [!tip]
> `ServletContext` - контекст сервлет-контейнера (Tomcat)

Ключевые особенности:

- Имеет доступ к `ServletContext`.
    
- Добавляет веб‑специфичные scope’ы:
    
    - `request` — один объект на HTTP‑запрос;
    
    - `session` — один объект на HTTP‑сессию;
       
    - `application` / `globalSession` (в кластерах).
       
- Используется Spring MVC‑инфраструктурой: `DispatcherServlet`, `HandlerMapping`, `HandlerAdapter`, `ViewResolver`, `HandlerExceptionResolver` и т.п. создаются именно в `WebApplicationContext`.


Поэтому:

- Если бину нужен `ServletContext`, request/session‑scope или интеграция с MVC‑инфраструктурой — ему нужен именно веб‑контекст.
    
- Бизнес‑логике (сервисы/DAO) достаточно обычного `ApplicationContext`; их часто кладут в root‑контекст, а веб‑контексты их наследуют.

## Зачем это разделение

Практические плюсы двухуровневой схемы:

- Разделение ответственностей:
    
    - root‑контекст — «ядро» приложения (бизнес‑логика, данные);
        
    - веб‑контекст(ы) — только веб‑слой.
        
- Возможность иметь несколько `DispatcherServlet` с разными конфигурациями (например, `api-servlet`, `admin-servlet`), но с общими сервисами/DAO.
    
- Более гибкая настройка scope’ов и жизненного цикла бинов.
    



----
#### [[WebApplicationContext - Spring MVC - Flashcards|Link to flashcards]]



---
### References:

-  [[Конфигурация WebApplicationContext - Spring MVC]]