
11-01-2026 09:35

Status:

Tags: [[SLF4J]] [[Logging]] [[Java+]]

---
# Underlying libraries configuration - SLF4J

Logback в default jar имеет api для взаимодействия с SLF4J. ~={cyan}Для него не нужно Adapter layer.=~

Если в classpath нет Logback -> нужна другая реалиизация + Adapter layer.

> [!tip] **Note**
> У SLF4J нет приоритета в выборе реализации логгирования. Он выбирает **единственную** реализацию. 
> 
> Если подключено сразу несколько -> ошибка


Если в приложении используются различные logging frameworks в различных зависимостях, то SLF4J их систематизирует, приводя логи к одному виду, указанному в настройках SLF4J. 
Для этого он использует библиотеки, подменяющие реализацию (типа `jcl-over-slf4j.jar`, которая заменяет `jcl-core.jar`) , которая перенаправляет логи в SLF4J, который в свою очередь перенаправляет их реализации.


![[Pasted image 20260111093944.png]]


> [!note]
> Jars типа `jcl-over-slf4j.jar`, `log4j-over-slf4j.jar` и т.п. именно **эмулируют API оригинальной библиотеки и перенаправляют вызовы**, а не «просто форматируют» уже созданные логи.



### Если используем как реализацию какую-то библиотеку (кроме Logback)

(Поверх SLF4J)

![[Pasted image 20260111094743.png]]

В SLF4J **adaptation layer** — это «прослойка» (binding), которая связывает абстрактный SLF4J‑API (`org.slf4j.Logger`) с конкретной реализацией логгера (Logback, Log4j, reload4j и т.п.). Он принимает вызовы `logger.info(...)` и переводит их в вызовы нужной библиотеки, не меняя код приложения.

`slf4j-reload4j.jar` — это адаптационный слой для **reload4j**:

> [!note]
> reload4j — форк старого Log4j 1.x, совместимый по API, но с исправлениями и поддержкой.

- `slf4j-reload4j` содержит реализацию backend’а SLF4J, которая направляет все вызовы SLF4J‑логгера в reload4j.
    
- Схема работы:
    
    - приложение использует только SLF4J (`Logger`, `LoggerFactory`);
        
    - в classpath лежит `slf4j-api` + `slf4j-reload4j` + собственно reload4j;
        
    - SLF4J находит `slf4j-reload4j` как реализацию и отправляет все сообщения в reload4j (дальше уже работают его appenders, конфиги `log4j.properties`/`log4j.xml` и т.п.).

> [!warning]
> Чтобы SLF4J работал, в classpath всегда должен быть **ровно один jar‑binding** вида `slf4j-XXX.jar`, который связывает SLF4J с конкретной реализацией логгера (Logback, Log4j2, JUL, reload4j, простой логгер и т.п.).
>
> **Примеры байндингов:**
> 1. **SLF4J + Logback**
>    
>    - jars: `slf4j-api` + `logback-classic` (внутри `logback-classic` уже есть binding для SLF4J).
>   
>    - Это по сути «стандартная» связка, но формально адаптер всё равно есть — просто он входит в Logback.
>   
>2. **SLF4J + Log4j2**
>    
>    - jars: `slf4j-api` + `log4j-slf4j-impl` + `log4j-core`.
>        
>    - `log4j-slf4j-impl` и есть adaptation layer от SLF4J к Log4j2.
>        
>3. **SLF4J + reload4j**
>    
>    - jars: `slf4j-api` + `slf4j-reload4j` + `reload4j`.
>        
>    - `slf4j-reload4j` — адаптер, который отправляет вызовы SLF4J в reload4j.
>        
>4. **SLF4J + JUL / simple / NOP**
>    
>    - `slf4j-jdk14` — адаптер к `java.util.logging`.
>        
>    - `slf4j-simple` — простая реализация, которая пишет в `System.err`.
>        
>    - `slf4j-nop` — «молчащий» backend, который глушит все логи.

----
#### [[Underlying libraries configuration - SLF4J - Flashcards|Link to flashcards]]



---
### References:

