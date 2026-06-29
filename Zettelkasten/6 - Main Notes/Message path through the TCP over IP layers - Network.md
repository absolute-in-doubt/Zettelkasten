
28-06-2026 13:03

Status:

Tags: [[Networks]]

---
# Message path through the TCP over IP layers - Network


![[Pasted image 20260628130047.png]]



### Physical

На этом уровне передаётся сообщение, которое представляет собой пакеты обёрнутые в абстракции и метаданные всех вышестоящих протоколов.

На физическом уровне данные передаются в виде нулей и единиц по коаксиальным кабелям, витой паре, оптоволокну в виде тока, света или радиосигналов


### Data Link (Канальный уровень)

![[Pasted image 20260628130549.png|426]]

#### MAC sublayer 

Оборачивает Ethernet frame метаданными:

- Header (Sender's MAC, Receiver's MAC)
- **IP packet**
- Trailer (4 error check bytes - для проверки целостности пакета получателем)


> MAC Address
> Это уникальный 6-bytes address встроенный в Network Interface Card (NIC) производителем 


#### Как Ethernet передает данные: CSMA/CD

Carrier Sense Multiple Access / Collision Detection - набор правил для передачи данных.

1. Перед тем как отправить данные по шине, компьютер слушает: свободна ли шина (не отправляет ли кто-то ещё данные в данный момент?)

2.1. Если шина свободна - компьютер начинает передачу данных

2.2. Если шина занята - компьютер ждет, пока она освободится

3. Если два компьютера начали отправку данных в один и тот же момент - происходит коллизия. В этом случае каждый компьютер останавливает передачу данных и ждёт рандомное количество времени, после чего повторяет попытку.


#### Error control

После каждого полученного Ethernet frame получатель отправляет ACK. 
Получатель проверяет eror-checking bytes (in the Trailer of the frame).
Если Ethternet frame повреждён, ACK также не отправляется и sender шлёт ARQ - Automatic Repeat Request



---
### Network (Сетевой уровень)


![[Pasted image 20260628132728.png]]

IP Packet:

- IP address получателя
- TCP packet/UDP Datagram


**Routing**

IP протокол используется для роутинга в глобальной сети. А MAC address резолвится уже в локальной сети при помощи ARP модуля (хз, чёйта)

**Path Determination**

Для нахождения оптимального пути в сети IP protocol использует протоколы:

- OSPF (Open Shortest Path First)
- BGP (Border Gateway Protocol)
- IS-IS (Intermediate System to Intermediate System)

 > [!note]
 > Поскольку routing происходит на L# OSI модели, Routers & Gateway иногда называют "L3 switches".
 
**IP не даёт надёжности:**

- не гарантирует доставку
- не проверяет ошибки
(Этим занимается транспортный уровень)


---
### Transport layer

![[Pasted image 20260628133900.png]]

> UDP не поддерживает сегментацию -> приложения должны слать небольшие сообщения, которые могут вместиться в одну UDP datagram (верхнеуровневые протоколы отвечают за сегметнацию)


----
#### [[Message path through the TCP over IP layers - Network - Flashcards|Link to flashcards]]



---
### References:

