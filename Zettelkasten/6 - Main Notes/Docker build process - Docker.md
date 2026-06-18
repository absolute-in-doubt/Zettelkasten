
04-02-2026 10:42

Status:

Tags: [[Docker]] [[Java+]]

---
# Docker build process - Docker

![[Pasted image 20260204104524.png]]

Dockerfile - содержит команды, необходимые для создания container image.

Build Context - обычно папка in your local filesystem (или ссылка на GitHub repository), содержащая исходный код приложения.

`.dockerignore` - используется для того, чтобы при сборке container image Docker проигнорировал определённые файлы.


### Docker build 

Для создания container image из Dockerfile + Build Context - вызываем `docker build` command:

```bash
docker build -f Dockerfile .
```

Here the `.` indicates that the current directory should be used as the build context.

**Note:** 
	You only have to pass a Dockerfile name if it is named something other than "Dockerfile"


### Building from Multi-Staged Dockerfiles

```dockerfile
# syntax=docker/dockerfile:1
FROM ubuntu AS base
RUN echo "base"

FROM base AS stage1
RUN echo "stage1"

FROM base AS stage2
RUN echo "stage2"
```

```bash
DOCKER_BUILDKIT=1 docker build --no-cache -f Dockerfile --target stage2 .
[+] Building 0.4s (7/7) FINISHED                                                   
 => [internal] load build definition from Dockerfile                                            0.0s
 => => transferring dockerfile: 36B                                                             0.0s
 => [internal] load .dockerignore                                                               0.0s
 => => transferring context: 2B                                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                0.0s
 => CACHED [base 1/2] FROM docker.io/library/ubuntu                                             0.0s
 => [base 2/2] RUN echo "base"                                                                  0.1s
 => [stage2 1/1] RUN echo "stage2"                                                              0.2s
 => exporting to image                                                                          0.0s
 => => exporting layers                                                                         0.0s
 => => writing image sha256:f55003b607cef37614f607f0728e6fd4d113a4bf7ef12210da338c716f2cfd15    0.0s
```


----
#### [[Docker build process - Docker - Flashcards|Link to flashcards]]



---
### References:

