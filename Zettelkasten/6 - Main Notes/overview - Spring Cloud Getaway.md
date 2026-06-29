
19-06-2026 16:24

Status:

Tags: [[Spring Cloud Getaway]] [[Spring Cloud]] [[Spring]]

---
# overview - Spring Cloud Getaway

Построен на основе Spring Boot, Spring WebFlux и Project Reactor.

Getaway предоставляет неблоккирующий асинхронный API-шлюз (reverse proxy). Использует Netty runtime предоставляемый Spring Boot и Spring WebFlux.

Spring Cloud Getaway позволяет быстро настроить API Getaway и даёт возможность писать свои расширения: фильтры, предикаты и т.д. Другие сервера такого предложить не могут.


----

**Route (маршрут)** - основной строительный блок Getaway. Он определяется идентификатором, целевым URI, набором **предикатов** и набором **фильтров**. 
**~={orange}Маршрут задействуется, если совокупный предикат истинен.=~**


**Downstream** - входящий запрос на API Getaway (из внешнего интернета).

**Upstreams** - выходящие потоки (внутри кластера - наши сервисы). туда роутим запросы.


**Фильтр** - это экземпляры `GetawayFilter`, созданные с помощью определённой фабрики. Здесь мы можем изменять запросы и ответы до или после отправки нисходящего запроса.


---

![[Pasted image 20260619163852.png]]





----
#### [[overview - Spring Cloud Getaway - Flashcards|Link to flashcards]]



---
### References:

