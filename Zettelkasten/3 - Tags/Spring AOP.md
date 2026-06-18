
Index: [[Spring]]

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

| Абстракция     | Описание                               | Аннотация                                                           |
| -------------- | -------------------------------------- | ------------------------------------------------------------------- |
| **Aspect**     | Модуль сквозной логики                 | `@Aspect`                                                           |
| **Pointcut**   | Выражение для выбора методов           | `@Pointcut("execution(* com.example.*.*(..)")`)                     |
| **Advice**     | Действие в точке среза                 | `@Before`, `@After`, `@AfterReturning`, `@AfterThrowing`, `@Around` |
| **Join Point** | Конкретный вызов метода                |                                                                     |
| **Proxy**      | JDK/CGLIB-прокси для перехвата вызовов |                                                                     |



- [[Возможности Spring AOP]]
- [[BeanPostProcessor proxifying vs Spring Aspects - Spring AOP]]


---
- [[Spring Core]] - про BPP