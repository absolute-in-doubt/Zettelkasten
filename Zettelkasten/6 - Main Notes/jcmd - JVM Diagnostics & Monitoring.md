
Index: [[JVM Diagnostics & Monitoring]]


Это утилита,, которая подключается к running JVM и предоставляет с нее данные, посредством вызова специальных методов JVM.

`jcmd` позволяет работать с:

- VM
	- VM Flags
	- System properties
	- Native memory
	
- Thread
	- PID
	- Thread Dumo
	- help
	- Attach
	- Diagnostics
	- Threads
	
- GC
	- Heap info
	- Class Histogram
	- Heap Dump
	
- JFR
	- JFR Recording
	- GC





### `jps`

Позволяет просмотреть список процессов JVM:

```bash
PS C:\java-workspace\hotels-rest\hotels-rest> jps
35312 Launcher
14356 Jps
18984 Launcher
3004 Main
35852 App # Это процесс главного класса моего приложения (то же название, что и у класса)
```

PS: _для точности можно в самом приложении проверить id процесса:_

```java
log.info("PID: " + ProcessHandle.current().pid());
```


### Подключение к Процессу JVM

Подключаемся по PID нашего приложения:

```bash
PS C:\java-workspace\hotels-rest\hotels-rest> jcmd 35852 help
35852:
The following commands are available:
Compiler.CodeHeap_Analytics
Compiler.codecache
Compiler.codelist
...
```


- [[VM commands - jcmd]]

- [[GC commands - jcmd]]

- [[Thread commands - jcmd]]
- [[Обнаружение Deadlocks через Thread Dump]]