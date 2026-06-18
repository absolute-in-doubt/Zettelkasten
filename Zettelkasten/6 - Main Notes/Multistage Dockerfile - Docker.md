
26-04-2026 09:24

Status:

Tags: [[Docker]] [[CI & CD]]

---
# Multistage Dockerfile - Docker



### Multi-Stage Dockerfile

[Multi-stage builds](https://docs.docker.com/build/building/multi-stage/) are a docker feature that helps to optimize container images by including multiple independent stages within a Dockerfile.

By splitting out separate development and production image stages we can have an ergonomic dev environment with dev dependencies, hot reloading, etc... but retain security and size improvements for deployment.

Shared steps can be built into a `base` stage and then customizations can be built on top of that base.

```dockerfile
#------------------------------------------- 
# Name the first stage "base" to reference later 
FROM node:19.6-bullseye-slim AS base 
#------------------------------------------- 
LABEL org.opencontainers.image.authors="sid@devopsdirective.com" 
WORKDIR /usr/src/app COPY package*.json ./ 
#------------------------------------------- 
# Use the base stage to create dev image 
FROM base AS dev 
#------------------------------------------- 
RUN --mount=type=cache,target=/usr/src/app/.npm \ 
	npm set cache /usr/src/app/.npm && \ 
	npm install 
COPY . . 
CMD ["npm", "run", "dev"] 
#------------------------------------------- 
# Use the base stage to create separate production image 
FROM base AS production 
#------------------------------------------- 
ENV NODE_ENV production 
RUN --mount=type=cache,target=/usr/src/app/.npm \ 
	npm set cache /usr/src/app/.npm && \ 
	npm ci --only=production 
USER node COPY --chown=node:node ./src/ . 
EXPOSE 3000 
CMD [ "node", "index.js" ]
```


##### Создание image из Multi-Staged dockerfile

```bash
docker build --target dev 
```



### Пример с Java

```dockerfile
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
#Определяет аргумент сборки `JAR_FILE`, который подставляется при `docker build`
ARG JAR_FILE=target/*.jar

# Копирует JAR-файл с хоста (по пути из `JAR_FILE`) в контейнер как `app.jar`
COPY ${JAR_FILE} app.jar 
EXPOSE 8080

# Задает неизменяемую точку входа: запускает JAR-файл командой `java -jar /app.jar`. Массивная форма предотвращает интерпретацию как shell-команды, обеспечивая прямой exec
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

Сборка:

```bash
docker build -t myapp:latest .
```

- `docker build` — запускает сборку образа по инструкциям из Dockerfile.
    
- `-t myapp:latest` — задает имя образа (`myapp`) и тег (`latest`). Можно использовать любое имя, например `my-spring-app:v1`.
    
- `.` (точка) — контекст сборки: текущая директория, содержащая Dockerfile и папку `target/` с JAR-файлом


Запуск:

```bash
docker run -p 8080:8080 my-java-app
```




----
#### [[Multistage Dockerfile - Docker - Flashcards|Link to flashcards]]



---
### References:

