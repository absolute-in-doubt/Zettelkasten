
13-01-2026 17:11

Status:

Tags: [[Docker]] [[Java+]]

---
# docker run options


# Common `docker run` Options

- `--rm`
  Automatically remove the container when it exits.

- `-d, --detach` 
  Run container in the background and print container ID.

- `--name`  
  Assign a name to the container.

- `-p, --publish <hostPort:containerPort>`  
  Publish container’s port(s) to the host. ~={cyan}Пробрасывает порт.=~

- `-e, --env <VAR=VALUE>`  
  Set environment variables.

- `-v, --volume <hostPath:containerPath> --mount source=<linuxVM_path>,destination=<container_path>`  
  [[Persistence - Docker#Создание Volume mounts|Mount a volume]] into the container.

- `--mount, --mount type=bind,source=<host_full_path>,destination=<container_path>`
  [[Persistence - Docker#Создание Bind mounts|Mount a bind mount]] into the container

- `-network <network>`  
  Connect the container to a specific network.

- `--restart <policy>` 
  Define restart policy (e.g., `no`, `always`, `on-failure`).

- `--tty, -t` 
  Выделяет псевдотерминал (TTY) и подключает его к стандартным потокам ввода/вывода контейнера, чтобы с ним было удобно работать из вашего терминала.

- `-it`  
  Run interactively with a TTY (`-i` = interactive, `-t` = pseudo-TTY).

- `--entrypoint <command>`  
  Override the container’s default entrypoint.

- `--workdir, -w <dir>`
  Set working directory inside the container.

- `--cpu-shares, --memory`
  Limit CPU or memory usage.

- `--hostname, -h <name>`
  Set a custom hostname.

- `--detach-keys`
  Override key sequence for detaching (default: `Ctrl+P, Ctrl+Q`).

- `--security-opt`
  Set security options like user namespaces or SELinux labels.



----
#### [[docker run options - Flashcards|Link to flashcards]]



---
### References:

