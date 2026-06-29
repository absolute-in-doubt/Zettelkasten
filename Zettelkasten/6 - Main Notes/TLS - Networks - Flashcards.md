
Theory for the cards: [[TLS - Networks]]

FILE TAGS: networks

Q: Как расшифровывается TLS?
A: Transport Layer Security
<!--ID: 1782666020675-->


Q: Почему шифрование и безопасность (TLS) добавили именно на уровне представления, а не сетевом уровне (IPSec) IP packets encryption или не замена TCP на транспортном уровне?
A: Потому что на сетевом уровне работают роутеры. Не все поддерживали IPSec и сложно было сделать защиту массовой. 
	
Попытки создать «безопасный TCP» были, но индустрия в итоге выбрала TLS как надстройку над TCP, потому что это оказалось гораздо более гибким, проще внедряемым и лучше соответствовало потребностям приложений.
	
Проблема в том, что транспортный уровень не знает многих вещей, необходимых приложению.
- Какой домен хотел посетить пользователь?
- Какая политика проверки сертификатов нужна?
- Нужно ли взаимное шифрование сообщений?
<!--ID: 1782666020685-->


Q: Упрощенно расскажи как работает TLS.
A: TLS использует сразу несколько видов криптографии.
	
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
	
- RSA - public key authentication mechanism
	
Actual data encryption (during the data exchange):
	
- AES 128 - AES encryption with **128-bit** size
	
- SHA 256 - hashing algorithm используется для взятия хэша для формирования signature
	
Данные передаются по принципу классического ассиметричного шифрования.
	
TLS Handshake:
```
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
	
**CA Signature** в сертификате сервера — это **цифровая подпись центра сертификации (CA)**, подтверждающая, что данный сертификат действительно был выпущен этим центром и не был изменён.
	
- Клиент генерирует временную пару ключей `(client private, client public)`.
- Сервер генерирует временную пару ключей `(server private, server public)`.
- Они **обмениваются только публичными ключами**.
- Затем каждая сторона, используя:
    
    - свой приватный временный ключ;
    - публичный временный ключ другой стороны,
    
    вычисляет общий секрет с помощью алгоритма **Diffie–Hellman** (обычно ECDHE).
    
	
```
shared_secret =ECDHE(my_private_key, peer_public_key)
```
	
Из этого `shared_secret` обе стороны независимо выводят:
	
- ключ шифрования lданных, передаваемых (клиент → сервер);
- ключ шифрования данных, передаваемых (сервер → клиент);
- ключи для проверки целостности;
- другие параметры сессии.
<!--ID: 1782666035845-->

Q: Что такое SNI?
A:   
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
<!--ID: 1782666714747-->



Q: Что даёт использование TLS?
A: **TLS обеспечивает:**
	
- Confidentiality
    
- Integrity
    
- Authentication
<!--ID: 1782666714752-->
