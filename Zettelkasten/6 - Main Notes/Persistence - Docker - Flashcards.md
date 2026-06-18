
Theory for the cards: [[Persistence - Docker]]

FILE TAGS: docker java_interview

Q: Describe the way of persisting the data of a docker container? без БД:) Опиши плюсы и минусы каждого подхода.
A: Сами Docker контейнеры, если сохраняют что-то в локальную память (директории внутри контейнера) - удаляют эти данные при завершении работы или падении контейнера.
	
Такие локальные директории называются `tmpfs mounts` - in memory storage.
	
**Ways of persisting container's data:**
	
1. Volumes -  data stored within LinuxVM (managed by Docker)
	
2. Bind mounts - location in your filesystem. (Папка из Windows привязывается к контейнеру)
	
- (For `bind mounts` performance can be much slower as it works between two systems => в приоритете использование `Volumes`)
- Удобно для разработки (кодим прямо в `bind mounts` и запускаем код в контейнере) 
<!--ID: 1770189251006-->
