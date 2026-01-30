
Theory for the cards: [[Happens before guarantee - Multithreading Java]]

FILE TAGS: multithreading java_interview

Q: Что такое visibility в многопоточности Java?
A: Visibility - видимость - то, что видят потоки в Heap. Visibility - то, что реально может прочитать поток.
<!--ID: 1769149916310-->


Q: Что такое happens before guarantee? Приведи пример.
A: Happens-before — это отношение «порядка» между действиями **в разных потоках**, гарантируемое Java Memory Model (JMM).
	
Happens before guarantee гласит о том, что если A happens-before B, то:
  - Все эффекты записи, сделанные до/в A, становятся видимыми потоку, выполняющему B.
  - B не может «увидеть» состояние памяти так, будто A не произошло.
	
> happens-before — логический порядок видимости, а не обязательно реальный порядок выполнения на процессоре.
<!--ID: 1769149916319-->


