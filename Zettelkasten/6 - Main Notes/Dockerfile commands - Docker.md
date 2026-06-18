
04-02-2026 10:46

Status:

Tags: [[Docker]] [[Java+]]

---
# Dockerfile commands - Docker

List of valid commands:  [Docker documentation](https://docs.docker.com/engine/reference/builder/)

```dockerfile
# This step installs dependencies 
RUN apt-get update && apt-get install -y <package_name>
```


| Instruction                                                                        | Description                                                 |
| ---------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| [`ADD`](https://docs.docker.com/reference/dockerfile#add)                          | Add local or remote files and directories.                  |
| [`ARG`](https://docs.docker.com/reference/dockerfile#arg)                          | Use build-time variables.                                   |
| [`CMD`](https://docs.docker.com/reference/dockerfile#cmd)                          | Specify default commands.                                   |
| [`COPY`](https://docs.docker.com/reference/dockerfile#copy)                        | Copy files and directories.                                 |
| [`ENTRYPOINT`](https://docs.docker.com/reference/dockerfile#entrypoint)            | Specify default executable.                                 |
| [`ENV`](https://docs.docker.com/reference/dockerfile#env)                          | Set environment variables.                                  |
| [`EXPOSE`](https://docs.docker.com/reference/dockerfile#expose)                    | Describe which ports your application is listening on.      |
| [`FROM`](https://docs.docker.com/reference/dockerfile#from)                        | Create a new build stage from a base image.                 |
| [`HEALTHCHECK`](https://docs.docker.com/reference/dockerfile#healthcheck)          | Check a container's health on startup.                      |
| [`LABEL`](https://docs.docker.com/reference/dockerfile#label)                      | Add metadata to an image.                                   |
| [`MAINTAINER`](https://docs.docker.com/reference/dockerfile#maintainer-deprecated) | Specify the author of an image.                             |
| [`ONBUILD`](https://docs.docker.com/reference/dockerfile#onbuild)                  | Specify instructions for when the image is used in a build. |
| [`RUN`](https://docs.docker.com/reference/dockerfile#run)                          | Execute build commands.                                     |
| [`SHELL`](https://docs.docker.com/reference/dockerfile#shell)                      | Set the default shell of an image.                          |
| [`STOPSIGNAL`](https://docs.docker.com/reference/dockerfile#stopsignal)            | Specify the system call signal for exiting a container.     |
| [`USER`](https://docs.docker.com/reference/dockerfile#user)                        | Set user and group ID.                                      |
| [`VOLUME`](https://docs.docker.com/reference/dockerfile#volume)                    | Create volume mounts.                                       |
| [`WORKDIR`](https://docs.docker.com/reference/dockerfile#workdir)                  | Change working directory.                                   |




---
### Common commands

```
FROM: Specifies the base layer or operating system for the container image.
RUN: Executes a command during the build phase.
COPY: Copies files from the build context (e.g., your local system) to the container image.
CMD: Provides a command to be executed when the container starts.
```

> [!tip]
> Каждая инструкция в `.dockerfile` создаёт новый слой внутри image. Docker кэширует эти слои для ускорения сборки.
>
> Т.е., если делаем так:
> ```dockerfile
> # копируем всё приложение
> COPY . .
> # подтягивание зависимостей
> RUN npm install
> ```
> То при изменении кода, весь кэшированный слой данной команды (и последующих) будет инвалидирован. И при следующей загрузке придётся заново ждать подгрузку зависимостей.
> Поэтому сначала грузим список зависимостей (аналог pom.xml):
> ```dockerfile
> # Copy only files required to install dependencies (better layer caching) 
> COPY package*.json ./ 
> RUN npm install 
> # Copy remaining source code AFTER installing dependencies. 
> # Again, copy only the necessary files 
> COPY ./src/ .
> ```

### Пример Dockerfile

```dockerfile
#------------------------------------------- 
# Pin specific version (use slim for reduced image size) 
FROM node:19.6-bullseye-slim 
#-------------------------------------------
# Use LABELS to provide additional info 
LABEL org.opencontainers.image.authors="sid@devopsdirective.com" 
#-------------------------------------------
# Set NODE_ENV 
ENV NODE_ENV production
#-------------------------------------------
# Specify working directory other than / 
WORKDIR /usr/src/app 
#-------------------------------------------
# Copy only files required to install dependencies (better layer caching) 
COPY package*.json ./ 
#------------------------------------------- 
# Use cache mount to speed up install of existing dependencies 
RUN --mount=type=cache,target=/usr/src/app/.npm \ 
	npm set cache /usr/src/app/.npm && \ 
	npm ci --only=production
#------------------------------------------- 
# Use non-root user 
# Use --chown on COPY commands to set file permissions 
USER node 
COPY --chown=node:node ./src/ .
#------------------------------------------- 
# Запуск
CMD [ "node", "index.js" ]
```


---
### LABEL

> [!Warning] Note
> LABEL adds metadata inside the image (like key-value annotations you can inspect with docker inspect). They have no effect on how the image is named or referenced.


```dockerfile
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL description="This text illustrates \
that label-values can span multiple lines."
```

Чтобы собрать image с определенным тегом, нужно указывать тег при сборке:
```bash
docker build -t enziberg-gateway:1.0 .
```

----
#### [[Dockerfile - Docker - Flashcards|Link to flashcards]]



---
### References:

