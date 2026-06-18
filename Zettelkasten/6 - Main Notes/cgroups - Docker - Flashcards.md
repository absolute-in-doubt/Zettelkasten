
Theory for the cards: [[cgroups - Docker ]]

FILE TAGS: docker java_interview

Q: Как достигается изоляция ресурсов сервера в docker контейнерах?
A: С помощью `cgroups` - это часть Linux. 
	
Позволяет группировать процессы и создавать иерархию групп. Эти группы можно мониторить и устанавливать им ограничения.
	
С помощью cgroups в container runtime (то, что запускает контейнер) может устанавливать следующие ограничения:
	
- Use up to XX% of CPU cycles (cpu.shares)
	
- Use up to YY MB Memory (memory.limit_in_bytes)
	
- Throttle reads to ZZ MB/s (blkio.throttle.read_bps_device)
<!--ID: 1770189388060-->
