
13-01-2026 13:19

Status:

Tags:

---
# Bare metal vs VM vs Container - Docker


### Bare metal

![[Pasted image 20260113132000.png]]

- Hellish dependency conflicts

- Low utilization efficiency

- Проблемы с одним приложением затронут и другие приложения на этом ПК

- Slow start up & shut down speed (minutes) of the PC

- Медленное масштабирование (придётся устанавливать дополнительное оборудование - сервера, компы)


---
### VMs

![[Pasted image 20260113132350.png]]

**Hypervisor** - выделяет ресурсы под виртуальные машины. Примеры:

![[Pasted image 20260113132540.png]]

**Type 1 hypervisor** - without underlying OS. Running directly on physical hardware (more efficient).

- No dependency conflicts
- Better utilization efficiency
- Small blast radius
- Faster startup and shutdown (minutes)
- Более быстрое масштабирование


---
### Container

![[Pasted image 20260113132934.png]]

- No dependency conflicts
- Even better utilization efficiency
- Small blast radius
- Even faster startup and shutdown (seconds)
- Еще быстрее масштабирование (seconds)
- Lightweight enough to use in development! ~={cyan}(can replicate production environment)=~

> [!note]
> Containers share kernel with the host -> worse isolation


---
### Tradeoffs

![[Pasted image 20260113133210.png]]



----
#### [[Bare metal vs VM vs Container - Docker - Flashcards|Link to flashcards]]



---
### References:

