
Theory for the cards: [[API rate limiting - System Design]]

FILE TAGS: system_design api

Q: Для чего нужен API rate limiter? Расскажи как он обычно реализуется.
A:    
![[Pasted image 20260324110150.png]]
	
1. Пользователь делает запрос к API Getaway;
2. API Getaway смоттрит в Redis по userId, есть ли у пользователя достаточное количество токенов, чтобы сделать запрос;
3. Если у пользователя достаточное количество токенов - делает запрос к серверу;
4. Если же недостаточно токенов - возвращает HTTP 429 Too Many Requests
	
5. Admin может вносить изменения в политику лимитов: сообщение об изменении ложим в броекр сообщений
6. Worker читает сообщения и обновляет (via an HTTP Request) политику лимитов (in-memory configuration of the API Getaway):
<!--ID: 1774339467332-->
