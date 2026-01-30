
13-01-2026 15:59

Status:

Tags: [[Docker]] [[Java+]]

---
# Docker Desktop Architecture

![[Pasted image 20260113155940.png]]

Docker Engine :
1. Docker CLI
2. Docker API + Docker Deamon (can run only on Linux)

### Docker client 
(перенаправляет запросы в docker deamon на Linux VM)

- _**Command Line Interface (CLI):**_ Interact with Docker using commands like`docker run` or `docker pull`.

- _**Graphical User Interface (GUI):**_ Browse images, configure CPU, memory, and disk space allocation.

- _**Credential Helper:**_ Store credentials for private registries.

- _**Extensions:**_ Third-party software that provides additional functionality.

### Linux VM
(On windows we can use as a backend:
1. ~={orange}WSL=~ 
2. Hyper-V as a virtualisation layer to create a virtual machine)

- **Docker daemon (dockerd):** Manages container objects, networking, and volumes within the server host application. It exposes the Docker API for communication with the client application.

- **(Optional) Kubernetes cluster**


----
#### [[Docker Desktop Architecture - Flashcards|Link to flashcards]]



---
### References:

