
02-04-2026 18:43

Status:

Tags: [[Docker]] [[CI & CD]]

---
# Подключение к запущенному контейнеру - Docker


#### 1. Подключение

```bash
docker attach [OPTIONS] CONTAINER
```



##### 2. Отключение (без остановки контейнера)

**Graceful Detach:** To disconnect without stopping the container, use the key sequence **`Ctrl + P` followed by `Ctrl + Q`** (only works if the container was started with `-it` flags).


##### 3. Отключение с остановкой

**Stopping the Container:** Pressing **`Ctrl + C`** typically sends a `SIGINT` (or `SIGKILL` depending on configuration) to the main process, which will stop the container

---
### Options

- `--no-stdin`: Attach to the output only (stdout/stderr) without sending input.

- `--detach-keys`: Specify a custom key sequence to detach from the container.

- `--sig-proxy`: If set to `false`, signals like `Ctrl + C` are not sent to the container's process.


---
### `attach` vs `exec`

| Feature           | `docker attach`                                                 | `docker exec`                                                        |
| ----------------- | --------------------------------------------------------------- | -------------------------------------------------------------------- |
| **Process**       | Connects to the **existing main process** (PID 1).              | Starts a **new, separate process** inside the container.             |
| **Exit Behavior** | Exiting often **stops the container**.                          | Exiting only stops the new process; the **container stays running**. |
| **Use Case**      | Monitoring logs or interacting with the app's main entry point. | Opening a new shell (e.g., `bash`) for debugging.                    |



----
#### [[Подключение к запущенному контейнеру - Docker - Flashcards|Link to flashcards]]



---
### References:

