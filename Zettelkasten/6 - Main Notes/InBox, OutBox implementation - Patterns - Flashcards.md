
Theory for the cards: [[InBox, OutBox implementation - Patterns]]

FILE TAGS: system_design kafka spring

Q: Что здесь не так?
![[Pasted image 20260415182613.png]]
A: Если здесь в `statisticsService.newEmployee()` будет выкинута ошибка, то откатить отправку сообщения мы не можем -> используем **Transactional OutBox:**
	
В методе `newEmployee(Employee employee)`:
- Сохраняем в `employeeRepository`
-  **~={orange}Атомарно сохраняем в outbox таблицу=~**
- выполняем  `statisticsService.newEmployee()` 
	
Таким образом, если  `statisticsService.newEmployee()` выкинет ошибку - просто откатим транзакцию записи в outbox таблицу.
<!--ID: 1776267056242-->

Q: Каким образом может осуществляться отправка событий из outbox таблицы? Как при этом избегать конкуренции нескольких инстансов сервисов?
A:  Из полностью no-code вариантов:
- Debezium CDC
- Kafka Connectors
Из более приземленных:
- запросы со `SKIP LOCKED`
- ShedLock
	
> `SKIP LOCKED` гарантирует только at-least once: сервис обработал сообщение, но неуспев закоммитить упал, после чего другой транзакции стали доступны эти данные -> отправлено дублирующее сообщение.  
> Чтобы избежать дубликации используем idempotency keys -> дубли отбросятся в inbox.
<!--ID: 1778686393846-->


