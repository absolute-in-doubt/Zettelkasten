
06-01-2026 10:59

Status:

Tags: [[Spring Core]] [[Spring]]

---
# Пакеты Spring Core


```xml
<dependency>  
  <groupId>org.springframework</groupId>  
  <artifactId>spring-context</artifactId>  
  <version>${spring-version}</version>  
</dependency>  
<dependency>  
  <groupId>org.springframework</groupId>  
  <artifactId>spring-beans</artifactId>  
  <version>${spring-version}</version>  
</dependency>  
<dependency>  
  <groupId>org.springframework</groupId>  
  <artifactId>spring-core</artifactId>  
  <version>${spring-version}</version>  
</dependency>
```


### Spring Core 

- Содержит реализацию базового IoC‑контейнера и механизмы внедрения зависимостей (DI).​
    
- Включает общие утилитные классы Spring (коллекции, работа с ресурсами, рефлексия и т.п.), которые используются всеми остальными модулями фреймворка


### Spring Beans

Этот модуль специализируется на описании и управлении бинами.​

- Содержит `BeanFactory` и связанные интерфейсы/классы для создания, конфигурирования и жизненного цикла объектов (бинов).​
    
- Отвечает за описание свойств бинов, их связывание (wiring), типизацию и обработчики, которые участвуют в инициализации/постобработке бинов.

### Spring Context

Это более «высокоуровневый» модуль, который строится на core и beans.
​
- Предоставляет `ApplicationContext`, расширяющий `BeanFactory`: события (event publishing), i18n, загрузка ресурсов, поддержка аннотаций, сканирование компонентов и т.п.​
    
- Включает интеграции и дополнительные сервисы уровня приложения: контекст для веба, поддержки профилей, окружений, а также доп. модуль `spring-context-support` для интеграции с кешированием, почтой, шаблонизаторами и др.

----
#### [[Пакеты Spring Core - Flashcards|Link to flashcards]]



---
### References:

