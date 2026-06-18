
13-01-2026 16:41

Status:

Tags: [[Docker]] [[Java+]]

---
# Persistence - Docker

Если контейнер (например с БД) упадёт, отключится и т.д., все данные будут потеряны -> необходима persistence.


При создании контейнера, всё, что создаётся из image - **read-only**. -> После создания контейнера создаётся **read-write layer**:

![[Pasted image 20260113164324.png]]


Если данные динамически добавляются в процессе работы контейнера и должны сохраняться даже при остановке контейнера  

![[Pasted image 20260113164758.png]]

`tmpfs mount` (нет на картинке) - обычные директории контейнера. Содержимое удаляется при остановке контейнера.~={cyan} (in memory)=~

`Volume` - data stored within LinuxVM (managed by Docker)

`Bind mounts` - location in your filesystem. (Папка из Windows привязывается к контейнеру)

- ~={cyan}(For `bind mounts` performance can be much slower as it works between two systems ~={red}=> в приоритете использование=~ `Volumes`)=~
- Удобно для разработки (кодим прямо в `bind mounts` и запускаем код в контейнере) 


##### Commands TL;DR

```bash
docker run -it --rm -v /host-path:/docker-path:ro <image_name>
```

- `/host-path` - путь к директории на Linux хосте (или WIndows, если это bind mount)

- `/docker-path` - путь, по которому директория будет доступна в контейнере

- `:ro` - **необязательный постфикс** - нужен для того, чтобы контейнер мог только читать из папки **(read only)**

---
### Создание Volume mounts:

```bash
# create a named volume 
docker volume create my-volume 

# view the list of volumes
docker volume list

# Create a container and mount the volume into the container filesystem 
docker run -it --rm --mount source=my-volume,destination=/my-data/ ubuntu:22.04 

# There is a similar (but shorter) syntax using -v which accomplishes the same 
docker run -it --rm -v my-volume:/my-data ubuntu:22.04


# Now we can create and store the file into the location we mounted the volume 
echo "Hello from the container!" > /my-data/hello.txt 
cat my-data/hello.txt 
exit
```

Теперь мы можем подключать эту `Volume mount` к разным контейнерам и использовать эти же файлы (`hello.txt`):

```bash
# Create a new container and mount the volume into the container filesystem 
docker run -it --rm --mount source=my-volume,destination=/my-data/ ubuntu:22.04 
cat my-data/hello.txt 
# successfully reads the file
exit
```


> [!note]
> Volumes are located in `/var/lib/docker/volumes` on LinuxVM.
> 
> Точное положение конкретной volume можно получить командой `docker inspect <volume_name>`

---
### Создание Bind mounts

```bash
# Create a container that mounts a directory from the host filesystem into the container 
docker run -it --rm --mount type=bind,source="${PWD}"/my-data,destination=/my-data ubuntu:22.04 

# Again, there is a similar (but shorter) syntax using -v which accomplishes the same 
docker run -it --rm -v ${PWD}/my-data:/my-data ubuntu:22.04 echo "Hello from the container!" > /my-data/hello.txt 

# You should also be able to see the hello.txt file on your host system 
cat my-data/hello.txt 
exit
```


----
#### [[Persistence - Docker - Flashcards|Link to flashcards]]



---
### References:

