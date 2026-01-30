
09-10-2025 17:41

Status: #not_even_born  

Tags: [[Patterns]]

---
# IoC Container

**IoC-контейнер** — это более общее понятие.  

> [!note] 
> [[DI Container|DI Container]] - это частный случай IoC-контейнера, который сфокусирован именно на:
>- **внедрении зависимостей** в объекты (через конструктор, поля или сеттеры),
  >  
>- разрешении зависимостей (Dependency Resolution),
 >   
>- управлении графом зависимостей.

Он отвечает за:

- создание объектов (бинов),
    
- управление их жизненным циклом,
    
- и **инверсию управления** — т.е. объекты не создают зависимости сами, а получают их от контейнера.
    

📌 Пример:  
Когда ты помечаешь класс `@Component`, Spring сам создаёт экземпляр этого класса и управляет им.  
Ты не вызываешь `new MyService()`, контейнер делает это за тебя.

👉 В Spring основной IoC-контейнер — это интерфейс `ApplicationContext` (или его реализации: `AnnotationConfigApplicationContext`, `ClassPathXmlApplicationContext` и т.д.).

----
#### [[IoC Container - Flashcards|Link to flashcards]]



---
### References:

- [[Inversion of control]]
- [[Application Context - Spring]]
