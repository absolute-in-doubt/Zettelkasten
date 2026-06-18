
23-03-2026 18:33

Status:

Tags: [[System Design]]

---
# Moving Data - System Design


### Оценка скорости передачи данных


- **Throughput** - пропускная способность
	- `RPS` - Requests Per Second - сколько ~={cyan}запросов клиента=~ сервер может обработать в секунду
	- `QPS` - Queries Per Second - сколько запросов может обработать ~={cyan}БД=~ в секунду
	- `B/s` - Bytes Per Second - сколько данных можем ~={cyan}передать по сети=~ или ~={cyan}обработать системой=~ за секунду
	
	
- **Latency** - задержка - время обработки одного запроса


> [!tip] Note
> Оптимизации одного из этих параметров обычно ведут к потерям в другом:
> - Добавление отправки батчами улучшит **Throughput**, но увеличит **Latency**.

----
#### [[Moving Data - System Design - Flashcards|Link to flashcards]]



---
### References:

