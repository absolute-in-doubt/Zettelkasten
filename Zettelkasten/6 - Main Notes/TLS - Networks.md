
28-06-2026 15:51

Status:

Tags: [[Networks]]

---
# TLS - Networks



TLS (**Transport Layer Security**) — криптографический протокол, обеспечивающий:

1. **Конфиденциальность** (никто не может прочитать данные).
    
2. **Целостность** (данные нельзя незаметно изменить).
    
3. **Аутентификацию** (можно удостовериться, что общаешься с настоящим сервером).
    

TLS используется в:

- HTTPS;
    
- SMTPS;
    
- FTPS;
    
- IMAPS;
    
- HTTP/2;
    
- HTTP/3 (встроен в QUIC).
   

---

# Основные криптографические примитивы TLS

TLS использует сразу несколько видов криптографии.

| Задача                 | Алгоритм                   |
| ---------------------- | -------------------------- |
| Аутентификация сервера | Асимметричная криптография |
| Обмен ключами          | ECDHE                      |
| Шифрование данных      | Симметричная криптография  |
| Проверка целостности   | AEAD/HMAC/SHA256           |

```
TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
```

Handshake part:

- ECDHE - Elliptic Curve Deffie-Hllman ephemeral - key exchange algorithm

- RSA - publick key authentication mechanism

Actual data encryption (during the data exchange):

- AES 128 - AES encryption with **128-bit** size

- SHA 256 - hashing algorithm используется для взятия хэша для формирования signature

Данные передаются по принципу классического [[Digital signatures & Assymetric encryption - Cryptography|ассиметричного шифрования]].



---

### Полная диаграмма TLS 1.2 handshake

```text
ClientHello
	- Maximum supported TLS version
	- Supported cipher suites (способ шифрования)
	- Client Random
	- Supported Groups (параметры ECDHE)
	- SNI
	- ALPN (позволяет договориться о протоколе прикладного уровня (http/1.1, http/2 и т.д.))
            ---->
	
            <----
ServerHello
	- Chosen TLS version
	- Chosen Cipher Suite (способ шифрования)
	- Server Random	
    +
server certificate
	- Domain Name
	- - Server RSA public key  
		(used to authenticate the server and verify  
		signatures in ServerKeyExchange)
	- CA Signature (Certification Authority Sign.)
	- Validity Period
    +
ServerKeyExchange  
	- Server ephemeral ECDHE public key  
	- Signature over:  
	(ClientRandom || ServerRandom || ECDHE public key)  
	signed with server RSA private key
    +
ServerHelloDone
	
	
	         ---->
ClientKeyExchange  
	- Client ephemeral ECDHE public key  
	  
	[Both sides compute the shared secret using ECDHE]
	
	+
ChangeCipherSpec (клиент готов к переходу на симметричное шифрование)
	+
Finished  
	- Symmetrically encrypted verify_data
	
	
			<-----
ChangeCipherSpec  (сервер готов к переходу на симметричное шифрование)
	+
Finished  
	- Symmetrically encrypted verify_data
```



> SNI (Server Name Indication) — это расширение TLS, позволяющее клиенту указать доменное имя, к которому он хочет подключиться, ещё на этапе TLS Handshake (т.к. первые HTTPS запросы обмена данных происходят после TLS handshake и сервер иначе не знал бы, какой сертификат ему нужно выдать). Это позволяет одному серверу с одним IP-адресом обслуживать несколько HTTPS-сайтов и выбирать правильный TLS-сертификат для каждого из них.
>
> Исторически SNI передавался **в открытом виде**.
>
> То есть провайдер мог видеть:
> ```
> IP: 203.0.113.10
> SNI: www.bank.com
> ```
> Хотя содержимое HTTPS было зашифровано.
>
>Для сокрытия этой информации был разработан механизм **ECH (Encrypted Client Hello)**, который шифрует SNI.


**CA Signature** в сертификате сервера — это **цифровая подпись центра сертификации (CA)**, подтверждающая, что данный сертификат действительно был выпущен этим центром и не был изменён.


---

#### Как работает ECDHE?

TLS 1.3 почти всегда использует:

> ECDHE (Elliptic Curve Diffie-Hellman Ephemeral)

Идея:

Клиент:

```text
private a
public A=g^a
```

Сервер:

```text
private b
public B=g^b
```

Обмен:

```text
A -> Server
B -> Client
```

Обе стороны вычисляют:

```text
Shared Secret = g^(ab)
```

Получается:

```text
Client Secret == Server Secret
```

При этом секрет не передаётся по сети.

---


### Forward Secrecy

Одно из важнейших свойств TLS 1.3.

Даже если злоумышленник украдёт:

```text
Server Private Key
```

он не сможет расшифровать старые соединения.

Потому что:

```text
Session Keys
```

были вычислены через:

```text
ECDHE
```

и уже уничтожены.

---

# Передача данных

После handshake создаются:

```text
Application Traffic Keys
```

Передача выглядит так:

```text
HTTP Request
      ↓
TLS Record
      ↓
Encryption
      ↓
TCP Segment
```

---

# TLS Record Layer

TLS разбивает данные приложения на:

```text
TLS Records
```

```text
HTTP Data
      ↓
+ TLS Header
+ Encryption
```

Пример:

```text
GET /index.html HTTP/1.1
```

становится:

```text
Encrypted TLS Record
```
    

---

# Диаграмма передачи данных

```text
Клиент                                         Сервер

HTTP Request
      ↓

Encrypt(TLS Key)

TLS Record ---------------------------------->

                                     Decrypt

                                     HTTP Request
```

---

# Проверка целостности

TLS использует:

```text
Authentication Tag
```

или ранее:

```text
HMAC
```

Если злоумышленник изменит хотя бы 1 бит:

```text
Integrity Check Failed
```

соединение будет разорвано.

---

# Возобновление сессии (Session Resumption)

После завершения соединения сервер может отправить:

```text
NewSessionTicket
```

Клиент сохраняет:

```text
PSK (Pre Shared Key)
```

При следующем подключении возможно:

```text
0-RTT
```

---

# Разрыв соединения

В TLS нет собственного сложного механизма закрытия как в TCP.

Используется сообщение:

```text
close_notify
```

Диаграмма:

```text
Клиент                                         Сервер

close_notify -------------------------------->

                         <---------------- close_notify
```

После этого:

```text
TCP FIN
```

закрывает транспортное соединение.

---

# Полная схема HTTPS

```text
Browser
    ↓
HTTP Request

HTTP
    ↓

TLS Encrypt

TLS Record
    ↓

TCP Segment
    ↓

IP Packet
    ↓

Ethernet Frame
```

На сервере:

```text
Ethernet
    ↓

IP
    ↓

TCP
    ↓

TLS Decrypt
    ↓

HTTP
```




**TLS обеспечивает:**

- Confidentiality
    
- Integrity
    
- Authentication




### Краткий ответ для интервью

> TLS — это криптографический протокол, обеспечивающий конфиденциальность, целостность и аутентификацию. Во время handshake стороны договариваются о версии TLS и cipher suite, выполняют обмен ключами с помощью ECDHE, сервер подтверждает свою личность сертификатом, после чего вычисляются симметричные session keys. Далее весь трафик передаётся в виде зашифрованных TLS Records, обычно с использованием AES-GCM или ChaCha20-Poly1305.

----
#### [[TLS - Networks - Flashcards|Link to flashcards]]



---
### References:

