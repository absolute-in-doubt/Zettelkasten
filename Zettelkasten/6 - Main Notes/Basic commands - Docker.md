
13-01-2026 17:23

Status:

Tags: [[Docker]] [[Java+]]

---
# Basic commands - Docker

- `docker run <...options...> <image_name>` - запуск контейнера. Про docker run options  [[docker run options|подробнее тут]]


- `docker start <container_name>`  - запуск существующего контейнера 


- `docker attach <container_name>` - подключает стандартный ввод, вывод и поток ошибок вашего терминала к уже запущенному контейнеру


- `docker pull <image>`: Download an image from a registry, like Docker Hub.


- `docker build -t <image_name> <path>`: Build an image from a Dockerfile, where `<path>` is the directory containing the Dockerfile.


#### `docker image`

- `docker image ls`: List all images available on your local machine.


- `docker image rm <image>`: Remove an image from your local machine.


#### `docker container`

- `docker container ls`: List all running containers.


- `docker container stop <container>`: Stop a running container.


- `docker container rm <container>`: Remove a stopped container.





----
#### [[Basic commands - Docker - Flashcards|Link to flashcards]]



---
### References:

