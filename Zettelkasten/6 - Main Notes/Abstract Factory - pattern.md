
14-02-2026 12:39

Status:

Tags: [[Patterns]]

---
# Abstract Factory - pattern

Предоставляет интерфейс для создания **семейств взаимосвязанных объектов** без указания их конкретных классов. Каждая реализация фабрики производит полную группу продуктов (например, GUI для Windows vs macOS).

![[Pasted image 20260214124020.png]]

Фактически то же самое, что и обычная фабрика. Но вместо одного объекта - предназначена для создания набора объектов одной области.

MacFactory - реализация фабрики, которая может создавать множество объектов (семейство элементов UI для Mac).
	
WinFactory - создаёт объекты из семейства элементов UI для Windows.

---
## Factory Method vs. Abstract Factory

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



----
#### [[Abstract Factory - pattern - Flashcards|Link to flashcards]]



---
### References:

