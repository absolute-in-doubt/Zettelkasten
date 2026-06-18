
Theory for the cards: [[REST standard]]

FILE TAGS: java_interview api system_design

Q: Какие HTTP методы, согласно REST, являются идемпотентными?
A:     
- `GET` — **idempotent**
	
- `POST` — **not idempotent
	
- `PUT` — **idempotent**
	
- `DELETE` — **idempotent**
	
- `PATCH` — may be idempotent, may be not.
	
- `HEAD` — **idempotent**
<!--ID: 1774087448838-->


Q: Как расшифровывается REST?
A: Representational State Transfer
<!--ID: 1774087448848-->

Q: Расскажи о минусах REST API.
A: Использование REST API может вести к:
- Over-fetching or Under-fetching of data - нет гибкости в указании того, какие и сколлько данных нам нужно
<!--ID: 1774281777073-->

