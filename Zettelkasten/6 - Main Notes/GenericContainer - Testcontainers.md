
12-06-2026 18:01

Status:

Tags: [[Testcontainers]] [[Testing]]

---
# GenericContainer - Testcontainers



Пример создания:

```java
GenericContainer<?> redis =
        new GenericContainer<>(DockerImageName.parse("redis:7.2"));
```

или

```java
GenericContainer<?> postgres =
        new GenericContainer<>(DockerImageName.parse("postgres:16"));
```

---

# GenericContainer

Полное имя: `org.testcontainers.containers.GenericContainer`


Это универсальный контейнер для любого Docker-образа.

**~={red}Все специализированные контейнеры (`PostgreSQLContainer`, `KafkaContainer`, `MongoDBContainer` и т.д.) внутри фактически наследуются от `GenericContainer`.=~**

Упрощённо:

```java
public class PostgreSQLContainer
        extends JdbcDatabaseContainer
        extends GenericContainer
```

---

## Основные методы настройки


### Порты:

#### expose port

```java
.withExposedPorts(5432)
```

После старта контейнера можно получить реальный порт:

```java
Integer port = container.getMappedPort(5432);
```

Пример:

```java
GenericContainer<?> redis =
        new GenericContainer<>("redis:7")
                .withExposedPorts(6379);
```

---

### Переменные окружения

```java
.withEnv("POSTGRES_USER", "admin")
.withEnv("POSTGRES_PASSWORD", "secret")
```

или

```java
Map<String, String> env = Map.of(
        "USER", "admin",
        "PASS", "secret"
);

.withEnv(env);
```

---

### Имя контейнера

```java
.withCreateContainerCmdModifier(cmd ->
        cmd.withName("my-container"));
```

Используется редко.

---

### Команда запуска

По умолчанию запускается ENTRYPOINT/CMD образа.

Можно переопределить:

```java
.withCommand("sleep", "3600")
```

или

```java
.withCommand("redis-server")
```

---

### Сетевые настройки

#### создать сеть

```java
Network network = Network.newNetwork();
```

#### подключить контейнер

```java
.withNetwork(network)
```

#### алиас внутри сети

```java
.withNetworkAliases("postgres")
```

Тогда другие контейнеры смогут обращаться:

```text
postgres:5432
```

---

### Файлы

#### копирование файла в контейнер

```java
.withCopyFileToContainer(
        MountableFile.forClasspathResource("init.sql"),
        "/tmp/init.sql"
)
```

---

### Bind mount

```java
.withFileSystemBind(
        "/host/path",
        "/container/path"
)
```

Используется редко, потому что ухудшает переносимость тестов.


---
## Wait strategies

Очень важная часть.

Контейнер может стартовать, но приложение внутри ещё не готово.

---

### ждать открытие порта

По умолчанию:

```java
Wait.forListeningPort()
```

---

### ждать HTTP endpoint

```java
.waitingFor(
        Wait.forHttp("/actuator/health")
                .forStatusCode(200)
)
```

---

### ждать строку в логах

```java
.waitingFor(
        Wait.forLogMessage(
                ".*Started.*",
                1
        )
)
```

---

### timeout ожидания

```java
.withStartupTimeout(Duration.ofMinutes(2))
```

---

# Работа с контейнером после запуска

## host

```java
String host = container.getHost();
```

Обычно:

```text
localhost
```

Но в CI может быть другой адрес.

---

## порт

```java
Integer port =
        container.getMappedPort(5432);
```

---

## id контейнера

```java
String id = container.getContainerId();
```

---

## имя контейнера

```java
String name = container.getContainerName();
```

---

# Логи

Получить все логи:

```java
String logs = container.getLogs();
```

---

Следить за логами:

```java
container.followOutput(
        new Slf4jLogConsumer(log)
);
```

---

# Lifecycle

## запуск

```java
container.start();
```

---

## остановка

```java
container.stop();
```

---

## проверка состояния

```java
container.isRunning();
```

---

# Выполнение команд внутри контейнера

Аналог:

```bash
docker exec
```

```java
Container.ExecResult result =
        container.execInContainer(
                "ls",
                "-la"
        );
```

Получение вывода:

```java
result.getStdout();
result.getStderr();
result.getExitCode();
```

---

# Reuse контейнера

Для ускорения тестов:

```java
.withReuse(true)
```

И в файле:

```properties
testcontainers.reuse.enable=true
```

Контейнер не будет пересоздаваться между запусками тестов.

---

# Наиболее часто используемые методы в реальных проектах

```java
new GenericContainer<>(DockerImageName.parse("redis:7"))
        .withExposedPorts(6379)
        .withEnv("KEY", "VALUE")
        .withNetwork(network)
        .withNetworkAliases("redis")
        .waitingFor(Wait.forListeningPort())
        .withStartupTimeout(Duration.ofMinutes(1));
```

И после запуска:

```java
container.start();

String host = container.getHost();
Integer port = container.getMappedPort(6379);
```

Это примерно 90% того, что используется при работе с `GenericContainer` в интеграционных тестах.

----
#### [[GenericContainer - Testcontainers - Flashcards|Link to flashcards]]



---
### References:

