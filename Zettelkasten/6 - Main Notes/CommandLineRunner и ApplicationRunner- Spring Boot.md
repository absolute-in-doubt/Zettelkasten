
09-01-2026 16:16

Status:

Tags: [[Spring]] [[Spring Boot]]

---
# CommandLineRunner - Spring Boot


`CommandLineRunner` и `ApplicationRunner` — это интерфейсы Spring Boot, которые позволяют выполнить произвольный код сразу после полной инициализации контекста приложения.

## Что это за интерфейсы

Оба интерфейса:

- принадлежат Spring Boot, пакет **`org.springframework.boot`**;
    
- имеют один метод `run(...)`;
    
- все бины, реализующие их, автоматически вызываются при старте после того, как поднят `ApplicationContext`.
    

## CommandLineRunner

- Интерфейс: `org.springframework.boot.CommandLineRunner`.
    
- Метод:
    
```java
void run(String... args) throws Exception;
```
    
- В `args` приходят аргументы командной строки как массив строк.
    

Пример:

```java
@Component
public class MyCommandLineRunner implements CommandLineRunner {
    @Override
    public void run(String... args) {
        System.out.println("App started, args: " + Arrays.toString(args));
    }
}
```
## ApplicationRunner

- Интерфейс: `org.springframework.boot.ApplicationRunner`.
    
- Метод:
	
```java
void run(ApplicationArguments args) throws Exception;
```
    
- Использует `ApplicationArguments` — обёртку над аргументами командной строки с удобными методами (`getOptionNames()`, `getOptionValues(...)`, `getNonOptionArgs()` и т.п.).
    

Пример:

```java
@Component
public class MyApplicationRunner implements ApplicationRunner {
    @Override
    public void run(ApplicationArguments args) {
        System.out.println("Options: " + args.getOptionNames());
        System.out.println("Non-option args: " + args.getNonOptionArgs());
    }
}
```

## Когда какой использовать

- Если нужны просто «сырые» аргументы как `String[]` — достаточно **`CommandLineRunner`**.
    
- Если нужно удобно работать с опциями вида `--flag=value` и разделять опции/неопции — удобнее **`ApplicationRunner`**, потому что `ApplicationArguments` уже всё парсит.


----
#### [[CommandLineRunner - Spring Boot - Flashcards|Link to flashcards]]



---
### References:

