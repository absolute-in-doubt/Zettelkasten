
23-03-2026 19:22

Status:

Tags: [[System Design]]

---
# Caching - System Design


> [!tip] Cache Ratio
> ![[Pasted image 20260323192538.png]]
> 
> Чем больше Cash Ratio - тем больше эффективность кэша. 
> Был ли результат запроса взят из кэша (Browser cache) можно понять из заголовка ответа:
> ![[Pasted image 20260323192709.png]]


Есть 4 типа кэширования:

- **Browser cache** - веб страница кэшируется на компьютере клиента
	Настраивается HTTP заголовком Cache-Control:
	![[Pasted image 20260323192426.png]]
	
- **Server Caching** - кэш ответов на запросы, хранимый в самом сервере (in-Memory) или в специальной отдельной БД (Redis, Memcache)
	
- **Database caching**  - кэш результатов запросов в самой БД или подключённой прослойке (Redis, Memcache).
	При запросе в БД - смотрим сначала в кэше, если там нет - только тогда начинаем выполнение:
	![[Pasted image 20260323193234.png]]
	Подходит для Read-Heavy Applilcations
	
- **[[CDN - System Design|CDN]] Cache:**
	 При отправке пользователем запроса, он перенаправлется к CDN server, если в кэше этого сервера нет нужного результата - делается запрос к Origin server:
	![[Pasted image 20260323193714.png]]
	


### Cache Eviction Policies

- LRU - Least Recently Used elements get deleted
- FIFO - First In First Out
- LFU - Least Frequently Used elements get deleted

----
#### [[Caching - System Design - Flashcards|Link to flashcards]]



---
### References:

- [[@Cacheable - Spring Data]]
