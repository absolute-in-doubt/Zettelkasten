
30-09-2025 10:49

Status: #baby

Tags: [[Java+]]

---
# SSH



- **SSH (Secure Shell)** — протокол для безопасного удалённого доступа к серверу.
- Используется для:
  - Подключения к удалённым системам
  - Передачи файлов (scp, sftp)
  - Туннелирования портов

---


Чтобы подключиться к серверу, нужно, чтобы на сервере был запущен SSHD (Open SSH Daemon)

```bash 
ssh brad@192.168.1.29
# brad - имя пользователя под которым входим
# 192.168.1.29 - ip адрес сервера (host name)
```

- Дополнительные параметры:

```bash
ssh -p 2222 user@hostname   # подключение к нестандартному порту
ssh -i ~/.ssh/id_rsa user@hostname   # использование ключа
```


### Authentication methods

- password - опасно, подвержен брут форсу
	
- public / private key pair
	
	```bash
	#generating keys
	ssh-keygen
	```
	- `~/.ssh/id_.rsa` - private key
	- `~/.ssh/id_rsa.pub` - public key
	- public key goes into server **"authorized_keys"** file
	
- Host based - файл список хостов, которым разрешено подключение



---
## 🔒 Безопасность

- **Используй ключи вместо паролей**:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
ssh-copy-id user@hostname
```


> [!note]
> Узнать hostname своего ПК (в PowerShell):
> ```powershell
> hostname
> ```

- **Отключи root-логин по паролю** в `/etc/ssh/sshd_config`:

```bash
PermitRootLogin no
PasswordAuthentication no
```

- **Меняй стандартный порт (22)** на другой, чтобы снизить риск атак:

```bash
Port 2222
```


- **Используй fail2ban или ufw/iptables** для защиты от брутфорса.

- **Регулярно обновляй OpenSSH** для устранения уязвимостей.



---
## 🛑 Закрытие соединения

- Внутри SSH-сессии:
    
    - Нажать `Ctrl+D` или ввести команду `exit`
        
- Сессия завершится, соединение будет закрыто.



---
## ⚙️ Дополнительные нюансы

- **Проброс портов (tunneling)**:

```bash
ssh -L 8080:localhost:80 user@hostname
```

Локальный порт `8080` будет перенаправлен на порт `80` удалённого сервера.

- **Обратный проброс**:

```bash
ssh -R 9090:localhost:3000 user@hostname
```

Удалённый сервер сможет обращаться к твоему локальному порту `3000`.

- **Динамический прокси (SOCKS5)**:

```bash
ssh -D 1080 user@hostname
```

Создаёт локальный SOCKS5-прокси на порту `1080`.

- **Конфигурационный файл** `~/.ssh/config` для удобства:

```config
Host myserver
	HostName example.com
	User neo
	Port 2222
	IdentityFile ~/.ssh/id_ed25519
```

Теперь подключение выполняется командой:

```bash
ssh myserver
```


## 📌 Итог

- **Открыть соединение** → `ssh user@host`

- **Закрыть соединение** → `exit` или `Ctrl+D`

- **Обезопасить** → ключи, отключение паролей, смена порта, firewall

- **Нюансы** → проброс портов, SOCKS5, конфиг для удобства

----
#### [[SSH - Flashcards|Link to flashcards]]



---
### References:


