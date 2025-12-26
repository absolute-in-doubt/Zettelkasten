
02-12-2025 11:28

Status:

Tags:

---
# Managing MinIO - MinIO

#### MinIO Set Up

> [!note] **Default credentials:**
> login = `minioadmin`
> 
> password = `minioadmin`


```bash
docker run -p 9000:9000 -p 9001:9001 --name <container_name>\
  quay.io/minio/minio server /data --console-address ":9001" --address ":9000"

 docker run -p 9000:9000 -p 9001:9001 --network minio-net --name myminio_server\
  quay.io/minio/minio server /data --console-address ":9001" --address ":9000"
```

---
### MinIO client

#### Создание Docker-сети

```bash
docker network create <network_name>

docker network create minio-net
```

#### Подключение контейнера к Docker-сети

Надо подключить minio server к этой сети

```bash
docker network connect <network_name> <container_name>

docker network connect minio-net myminio_server
```



Запуск клинета (отдельный Docker image):

```bash
docker run 
--rm # автоматически удаляет контейнер после того, как процесс внутри него завершится.
-i # оставляет STDIN открытым (можно вводить команды)
-t # выделяет псевдо-TTY (делает интерфейс интерактивным, удобным для CLI)
--network <network_name> # подключает создаваемый контейнер к указанной Docker-сети
 minio/mc <command>
  
  
docker run --rm -it --network minio-net minio/mc ls
```
Если указать просто `docker run --rm -it --network minio-net minio/mc` - выведет список комманд


Здесь 

- `minio/mc` — официальный образ клиента.

~={pink}Чёт не подрубается :) -> юзай web console=~


---
### MinIO web console 

Просто переходим на `http://localhost:9001` в браузере

Позволяет только создавать private buckets и добавлять в них файлы.

Не позволяет сохдавать public buckets и управлять доступом.

----
#### [[Managing MinIO - MinIO - Flashcards|Link to flashcards]]



---
### References:

