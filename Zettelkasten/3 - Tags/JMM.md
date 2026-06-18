Index: [[Java Core]]


![[Pasted image 20260116102607.png]]
(Это для JVM 7. PermGen больше нет. String Pool - в Heap. Mehod Area - сам по себе)

> [!tip] Note
> В контексте Java под байткодом обычно имеют в виду именно **набор инструкций виртуальной машины Java**, которые находятся в `.class`‑файлах и интерпретируются/компилируются JVM, а не конкретным физическим процессором.
>
> байткод = код инструкций JVM


1. [[Detailed Initialization Procedure - Java]]
    - [[clinit method - Java]]
2. [[ClassLoader - JMM]]

2. [[StringPool - JMM]]
3. [[PCR - JMM]]
4. [[Heap - JMM]]
5. [[Method Area (Metaspace) - JMM]]
	 - [[Run-time constant pool - JMM]]
6. [[Stack - JMM]]
	- [[Native stack - JMM]]
	- [[Frames - JMM]]
7. [[JIT, Code cache space - JMM]]
8. [[Garbage Collector - JMM]]
	 - [[Generational Garbage Collecting - JMM]]
	 - [[Виды GC в HotSpot JVM - JMM]]

- [[Execution Engine - JVM]]

#### Подробнее с примерами байткода (для JVM 7): https://habr.com/ru/articles/739338/

---
- [[Multithreading]]