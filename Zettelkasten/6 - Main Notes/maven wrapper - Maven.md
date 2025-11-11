
09-11-2025 09:19

Status:

Tags: [[Maven]]

---
# maven wrapper - Maven

Скрипт `mvnw` (или `mvnw.cmd` на Windows) — это **обёртка над Maven**, которая гарантирует, что проект будет собран с нужной версией Maven, даже если она не установлена в системе.

> [!note]
> Просто автоматизирует (для CI/CD) и унифицирует версию Maven. 
> 
> Без JRE работать не будет.

---

```bash
mvn -N io.takari:maven:wrapper
```

### 📦 Что делает эта команда:

- Загружает плагин `takari-maven-plugin`, который умеет генерировать wrapper.
    
- Создаёт файлы:
    
    - `mvnw` (для Unix/Linux/macOS)
    
    - `mvnw.cmd` (для Windows)
       
    - `.mvn/wrapper/maven-wrapper.jar`
       
    - `.mvn/wrapper/maven-wrapper.properties`

Ключ ~={yellow}**-N**=~ означает "не рекурсивно", то есть команда применяется только к текущему проекту, без захода в подпроекты.


---
### ⚙️ Что делает `mvnw` при запуске:

1. **Проверяет наличие Maven Wrapper JAR** Ищет файл `.mvn/wrapper/maven-wrapper.jar`. Это основной исполняемый файл, который управляет загрузкой Maven.

2. **Проверяет наличие нужной версии Maven** Смотрит в `.mvn/wrapper/maven-wrapper.properties`, где указана ссылка на нужную версию Maven:

```properties
distributionUrl=https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.9.6/apache-maven-3.9.6-bin.zip
```

2. **Если Maven ещё не скачан — скачивает его** Загружает Maven в папку `~/.m2/wrapper/dists/...` (или аналогичную временную директорию), распаковывает и кэширует.

3. **Запускает Maven с нужной версией** Передаёт все аргументы, которые ты указал (`clean install`, `test`, и т.д.), в скачанный Maven, и запускает его как будто ты вызвал `mvn`.


----
#### [[maven wrapper - Maven - Flashcards|Link to flashcards]]



---
### References:

