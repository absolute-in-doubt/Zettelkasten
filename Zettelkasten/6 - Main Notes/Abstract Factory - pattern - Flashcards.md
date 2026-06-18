
Theory for the cards: [[Abstract Factory - pattern]]

FILE TAGS: java_interview patterns

Q: Чем отличаются Factory/Factory Method и Abstract Factory?
A:    
1. **Purpose:** 
- Factory Method/Factory: Создаёт один тип объектов.
- Abstract Factory: Создаёт семейства связанных объектов.
	
	
2. **Simple Example:** 
- Factory Method: A CarFactory creates one type of car.
- Abstract Factory: A VehicleFactory creates cars, trucks, and bikes of the same brand.
	
	
3. **Flexibility:** 
-  Factory Method: Adding new products requires changing the factory.
- Abstract Factory: Adding new families doesn't affect existing code.
	
	
4. **Use Case:** 
- Factory Method: When you need to create a single object (e.g., one car model).
- Abstract Factory: When you need to create related objects (e.g., different vehicles from the same brand).
<!--ID: 1771064002950-->




Q: Что такое Abstract Factory? Для чего она нужны? Приведи пример.
A:   
Предоставляет интерфейс для создания **семейств взаимосвязанных объектов** без указания их конкретных классов. Каждая реализация фабрики производит полную группу продуктов (например, GUI для Windows vs macOS).
	
![[Pasted image 20260214124020.png]]
	
Фактически то же самое, что и обычная фабрика. Но вместо одного объекта - предназначена для создания набора объектов одной области.
	
MacFactory - реализация фабрики, которая может создавать множество объектов (семейство элементов UI для Mac).
	
WinFactory - создаёт объекты из семейства элементов UI для Windows.
	
---
**Пример:**
	
В какой-то мере фабрики `BeanFactory` также будут являться и абстрактными фабриками, если мы используем профили:
	
```java
@Configuration
@Profile("dev")  // ConcreteFactory1
public class DevConfig { 
    @Bean public DataSource h2DataSource() { ... }
    @Bean public JdbcTemplate jdbcTemplate(DataSource ds) { ... }
}
	
@Configuration  
@Profile("prod")  // ConcreteFactory2  
public class ProdConfig { 
    @Bean public DataSource postgresDataSource() { ... }
    @Bean public JdbcTemplate jdbcTemplate(DataSource ds) { ... }
}
```
	
Т.е. при запуске с профилем dev будет создана реализация абстрактной фабрики, создающая объекты из семейства бинов, определённых с `@Profile("dev")` - будут создан объекты для работы с H2 DB, например.
	
А при запуске с профилем prod - будет создана реализация абстрактной фабрики, создающая бины, определённые с `@Profile("prod")` - будут созданы объекты для работы с Postgres.
<!--ID: 1771063963710-->
