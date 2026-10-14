
12-09-2026 14:00

Status:

Tags: [[Python]]

---
# Venv - Python


Предназначен для того, чтобы зависимости не были доступны глобально, а импортировались исключительно для нашего проекта.


### 1. Создание venv

```shell
python -m venv .venv
```

- `.venv` - имя файла, где будет храниться virtual environment.

> [!note]
> Точка перед именем файла делает его скрытым в файловой системе. Но для создания venv это не обязательно. 
> Можно просто так:
> ```shell
> python -m venv myvenv
> ```


---
### 2. Активировать окружение

**CMD:**

```bash
.venv\Scripts\activate.bat
```

**PowerShell:**

```bash
.venv\Scripts\Activate.ps1
```


---
### 3. Установить библиотеку в venv

```bash
pip install requests
pip install -r requirements.txt
```



---
### 4. Запустить программу через venv

**С активированным окружением:**

```bash
python main.py
```

**Без активации:**

```bash
.venv\Scripts\python.exe main.py
```

### 5. Деактивировать окружение

```bash
deactivate
```






----
#### [[Venv - Python - Flashcards|Link to flashcards]]



---
### References:

