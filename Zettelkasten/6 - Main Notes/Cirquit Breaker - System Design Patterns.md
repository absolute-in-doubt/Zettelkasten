
14-05-2026 07:42

Status:

Tags: [[System Design]] [[Patterns]]

---
# Cirquit Breaker - System Design Patterns

![[Pasted image 20260514074413.png]]

- Сервис замечает, что другой сервис тормозит

- На какое-то время он перестаёт посылать запросы этому инстансу

Восстановление работы:
- автоматически через какое-то время
- вручную включаем cirquit breaker back on


Если cirquit breaker находится в отключенном состоянии (Например Movie Info Service тормозит и Movie Cataalog Service отключил cirquit breaker -> Movie Info Service временно недоступен) -> если к Movie Catalog Service приходит запрос, требующий запрос к Movie Info Service, то он просто вернет ошибку.
	Нужно это, чтобы не замедлять другую функциональность Movie Info сервиса


### What triggers Cirquit Breaker?

##### Last N requests

 ![[Pasted image 20260514075524.png|336]]

- Если один запрос фейлится - похуй

- Берем 5 последних запросов, если 3 из них failed -> break the Cirquit


----
![[Pasted image 20260514080143.png]]

Последний параметр - сколько времени ждем, прежде чем включить Cirquit Breaker обратно

> [!warning] 
> Зачастую после sleep timeout состояние Cirquit Breaker - Half Open: Он дает пройти только нескольким запросам, чтобы проверить, все ли окей.
> И только если все окей, возвращется к нормальному функционированию



![[Pasted image 20260514080359.png]]


---
### Поведение, если downstream service unavailable (Cirquit Breaker is off)


- throw an error 

- return a fallback "default" response

- Кэшировать ответы другого сервиса, и если тот недоступен - доставать отвеет из кэша


---
### Плючы и минусы

- Failing fast - если что-то хуево работает - узнаем об этом почти сразу


- Fallback functionality intended

- Automatic recovery


----
#### [[Cirquit Breaker - System Design Patterns - Flashcards|Link to flashcards]]



---
### References:

