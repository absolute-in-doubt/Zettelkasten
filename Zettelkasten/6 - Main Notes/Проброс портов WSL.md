
30-11-2025 15:25

Status:

Tags: [[Local network]] [[Java+]]

---
# Проброс портов WSL


Через Powershell:
```powershell
netsh interface portproxy add v4tov4 listenport=3000 listenaddress=0.0.0.0 connectport=3000 connectaddress=<WSL_IP>
```

Где `<WSL_IP>` - ip of the WSL virtual machine (обычно что-то вроде `172.x.x.x`).

~={pink}Как узнать?=~
	 В wsl - `ip addr`:
	  inet ~={orange}172.20.98.210=~/20 brd 172.20.111.255 - нам нужен тот, что inet


Тут пробрасываем порт 3000.


## Остановка проброса порта

```powershell
netsh interface portproxy delete v4tov4 listenport=3000 listenaddress=0.0.0.0
```



----
#### [[Проброс портов WSL - Flashcards|Link to flashcards]]



---
### References:

