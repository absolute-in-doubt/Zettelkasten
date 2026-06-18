
Theory for the cards: [[Spring Batch overview]]

FILE TAGS: spring_batch spring


Q: Расскажи об основных абстракциях Spring Batch.
A:   
![[Pasted image 20260418113108.png]]
	
	
- **Job Launcher** - запускает Job
	
- **Tasklet** - отвечает за выполнение работы
	
- **JobRepository** - хранит в служебных таблицах БД задания _Job_, попытки их выполнения _JobExecution_ и многое другое — всего 6 таблиц.
<!--ID: 1777353177727-->


Q: Почему Spring Batch плохо совместим с обработкой бесконечного количества данных, например из message broker?
A:    
1. Spring Batch читает батчами: может 10 минут слушать и только потом начнет работать.
	
2. **ItemReader должен возвращать `null` для завершения**: Spring Batch ожидает, что Reader завершит чтение и вернёт `null`. С message broker'ом это невозможно — сообщения приходят непрерывно.
    
3. **Job имеет конечное состояние**: `JobInstance` завершается со статусом `COMPLETED`/`FAILED`. Нельзя "перезапустить" бесконечный consumer.
    
4. **JobRepository overhead**: Метаданные (StepExecution, chunk count) пишутся в БД для каждого chunk'а — неэффективно для high-throughput stream'ов.
    
5. **Chunk-oriented модель**: Предполагает обработку фиксированного объёма данных за транзакцию, а не streaming.
<!--ID: 1777353187406-->
