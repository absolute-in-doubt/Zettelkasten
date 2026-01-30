
Theory for the cards: [[Уровни логгирования]]

FILE TAGS: logging java_interview

Q: Какие есть уровни логгирования? Приведи пример того, какие логи будут вывоиться при установке уровня логгирования `INFO`.
A:   
	
| Level     | `intLevel`          | Description                                               |
| --------- | ------------------- | --------------------------------------------------------- |
| **ALL**   | `Integer.MAX_VALUE` | Enables all logging.                                      |
| **TRACE** | `600`               | Very detailed diagnostic information.                     |
| **DEBUG** | `500`               | Fine-grained events for debugging.                        |
| **INFO**  | `400`               | Informational messages on application progress.           |
| **WARN**  | `300`               | Potentially harmful situations.                           |
| **ERROR** | `200`               | Error events that may allow the application to continue.  |
| **FATAL** | `100`               | Very severe error events likely to abort the application. |
| **OFF**   | `0`                 | Turns off all logging.                                    |
	
- A logger set to `INFO` logs messages at `INFO`, `WARN`, `ERROR`, and `FATAL` levels, but not `DEBUG` or `TRACE`.
	
- A logger set to `WARN` logs only messages at `WARN`, `ERROR`, and `FATAL` levels.
	
- Setting the logger level to `TRACE` or `ALL` logs every message. 
<!--ID: 1769420251918-->
