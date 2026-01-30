
04-10-2025 16:00

Status: #baby 

Tags: [[Spring]]

---
# Inversion of control

**Inversion of Control** = _“переворот управления”_.  **Приложение не само управляет своим ЖЦ и зависимостями, а передаёт это управление фрейворку.** 
~={orange}То, что мы регистрируем бины - передаём управление ими фреймворку.=~


Идея в том, что **объекты не сами создают и связывают свои зависимости**, а **получают их извне**.

В “обычном” коде (без IoC) объект сам решает, какие зависимости создавать:

```java
public class UserService {
     private UserRepository repo = new UserRepository(); // жёсткая зависимость 
}
```

Здесь **`UserService` контролирует** создание зависимости `UserRepository`.

---

### Как это работает в Spring

В Spring используется **[[IoC Container|IoC Container]]** (контейнер инверсии управления, чаще его называют просто **ApplicationContext**).

- Ты описываешь **что за объекты (beans)** нужны (через `@Component`, `@Bean`, XML и т.д.).
    
- Контейнер **создаёт эти объекты** (управляет их жизненным циклом: создание, инициализация, уничтожение).
    
- Контейнер **“внедряет” зависимости** (Dependency Injection — частный случай IoC).
    

Пример в стиле Spring Boot:

```java
@Service 
public class UserService {
     private final UserRepository repo;      // Spring сам подставит бин UserRepository сюда     
     public UserService(UserRepository repo) {
		  this.repo = repo;     
	} 
}
```

Здесь `UserService` **не знает**, как создаётся `UserRepository`.  
Spring сам:

1. найдёт bean `UserRepository`
    
2. создаст его (если ещё не создан)
    
3. передаст его в конструктор `UserService`.
    

Таким образом, контроль над процессом **передан контейнеру** — и это и есть _Inversion of Control_.

---

## 🔹 IoC vs Dependency Injection

- **IoC** — более общий принцип: _“объект не управляет сам своей жизнью и зависимостями, это делает контейнер”_.
    
- **Dependency Injection (DI)** — конкретная реализация IoC: зависимости передаются объекту извне (через конструктор, сеттер, поле).




----
#### [[Inversion of control - Flashcards|Link to flashcards]]



---
### References:

- [[Dependency Injection]]