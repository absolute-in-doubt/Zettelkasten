
26-02-2026 13:23

Status:

Tags: [[Spring Boot]] [[Spring]]

---
# Аннотации конфигурации - Spring Boot


## Основные аннотации

- **~={purple}@SpringBootApplication=~**: Комплексная аннотация для главного класса приложения. 
- Состоит из: 
	- **~={purple}@SpringBootConfiguration=~**, 
	- **~={purple}@EnableAutoConfiguration=~**, 
	- **~={purple}@ComponentScan=~**. 
  Автоматически сканирует компоненты в пакете и подпакетах, включает автоконфигурацию на основе зависимостей.
    
- **~={purple}@SpringBootConfiguration=~**: Метаданные для конфигурационного класса в Spring Boot. Наследует ~={purple}@Configuration=~, но предназначена специально для Boot-приложений. Используется в главном классе вместо ~={purple}@Configuration=~ для явного указания Boot-конфигурации.
  ~={cyan}Рекомендуется одна на приложение=~.
	
> [!example] Note
> Комбинировать `@SpringBootApplication` 
> и `@SpringBootConfiguration` одновременно не имеет смысла и может привести к дублированию метаданных.

    
- **~={purple}@SpringBootTest=~**: Аннотация для интеграционных тестов. Загружает полный ApplicationContext, ищет основной класс с ~={purple}**@SpringBootApplication**=~ или **~={purple}@SpringBootConfiguration=~**. Поддерживает свойства, профили и веб-окружение.


- **~={purple}@TestConfiguration=~** - Альтернативная приоритетная конфигурация для тестов.
    

##### Когда использовать @SpringBootConfiguration, а когда - просто указать бины в классе @SpringBootApplication

   
- **Бины в @SpringBootApplication классе**: Допустимо для простых приложений с 1-2 бинами в главном классе (main-метод). Удобно, если логика конфигурации минимальна и связана с запуском.
    
- **Лучшие практики**: Избегайте перегрузки главного класса — выносите сложные бины в отдельные @Configuration/@SpringBootConfiguration классы. В больших проектах используйте @Configuration для доменных конфигураций, @SpringBootConfiguration — только для корневой.


----
#### [[Аннотации конфигурации - Spring Boot - Flashcards|Link to flashcards]]



---
### References:

