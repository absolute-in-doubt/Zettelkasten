
Theory for the cards: [[Observability]]

FILE TAGS: observability java_middle_interview

Q: Чем мониторинг отличается от Observability?
A:  Monitoring - фокусируется именно на метриках, алертах, анализе с дашбордами. Т.е. мониторинг - часть observability.
	
Observability - three pillars:
- Metrics - исторические данные о системе (CPU, memory, disk usage, network)
	- alerts - для быстрого реагирования (сообщения в бота в ТГ)
	- dashboards (Grafana) - представление метрик для удобства анализа
- Logs 
- Traces - пути запроса (особенно эффективно для кластеров), например: LoadBalancer -> Frontend -> Backend -> Database 
<!--ID: 1773502706297-->


Q: Для чего нужно Observability?
A:  Observability помогает  отслеживать выполнение SLA (Service Level Agreement), например, отслеживать availability приложения. И, в случае возникновения проблем, позволяет оперативно реагировать на инцеденты, а также отслеживать узкие места и быстрее находить баги.
	
- метрики для SLA
- узкие места и оптимизация
- быстрое реагирование на инциденты
- удобное нахождение багов (Logs, Tracing)
<!--ID: 1773502706307-->
