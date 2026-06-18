
13-01-2026 15:39

Status:

Tags: [[Docker]]

---
# cgroups - Docker

~={cyan}helps to isolate containers' performance=~. Так, чтобы одно приложение (контейнер) не съедал все ресурсы, не оставляя ничего другому.


Позволяет группировать процессы и создавать иерархию групп. Эти группы можно мониторить и устанавливать им ограничения.

С помощью cgroups в container runtime (то, что запускает контейнер) может устанавливать следующие ограничения:

- Use up to XX% of CPU cycles (cpu.shares)

- Use up to YY MB Memory (memory.limit_in_bytes)

- Throttle reads to ZZ MB/s (blkio.throttle.read_bps_device)


----
#### [[cgroups - Docker - Flashcards|Link to flashcards]]



---
### References:

