
24-09-2026 19:17

Status:

Tags: [[JVM Diagnostics & Monitoring]] [[Java+]]

---
# Запуск java программы из командной строки - аргрументы и флаги - JVM Diagnostics & Monitoring


Общий вид:

```
java [JVM options] [system properties] [agents] -cp <classpath> <MainClass> [application args]
```

Например:

```bash
java ^
  -Xmx4g ^
  -XX:+UseG1GC ^
  -XX:CICompilerCount=12 ^
  -Dfile.encoding=UTF-8 ^
  -javaagent:"C:\Program Files\JetBrains\IntelliJ IDEA Community Edition 2024.3.1\lib\idea_rt.jar=54306" ^
  -cp target\classes com.example.Main ^
  arg1 arg2
```


---
### Типы аргументов командной строки


**1. JVM flags**

Они передаются непосредственно JVM:

```bash
-Xmx4g
-XX:+UseG1GC
-XX:CICompilerCount=12
-XX:ConcGCThreads=3
-XX:G1HeapRegionSize=2097152
```

Они должны идти **до имени главного класса**:

```bash
java -XX:+UseG1GC -Xmx4g com.example.Main
```

> [!warning] **Note**
> `-XX:CICompilerCount` - это **количество потоков [[JIT, Code cache space - JMM|JIT-компилятора]]**, которые JVM выделяет для компиляции Java-кода в машинный код.


---
**2. System properties**

Это тоже параметры JVM, хотя синтаксически они начинаются с `-D`:

```bash
-Dfile.encoding=UTF-8
-Dsun.stdout.encoding=UTF-8
-Dsun.stderr.encoding=UTF-8
```

Например:

```bash
java -Dfile.encoding=UTF-8 com.example.Main
```

В Java они доступны через:

```java
System.getProperty("file.encoding");
```


---
**3. `-javaagent`**

Это также параметр запуска JVM:

```bash
-javaagent:C:\...\idea_rt.jar=54306
```

Он говорит JVM загрузить Java Agent перед запуском приложения.

Для запуска **из IntelliJ** он обычно нужен самой IntelliJ для debugger/run integration.

Если ты запускаешь программу самостоятельно из обычного `cmd`/PowerShell, **`idea_rt.jar` обычно не нужен**.

---

### Аргументы самого приложения

Например:

```bash
java -Xmx4g com.example.Main hello 123
```

Здесь: 
- `-Xmx4g`→ JVM
- `com.example.Main`→ главный класс
- `hello 123` → `args[]` твоего приложения.

То есть:

```java
public static void main(String[] args) {
    System.out.println(Arrays.toString(args));
}
```

получит: `[hello, 123]`


#### Еще пример:

```bash
java -Xmx4g -XX:+UseG1GC -Dfile.encoding=UTF-8 -cp app.jar com.example.Main --port 8080
```

Здесь `--port 8080` уже **не JVM**, а аргументы приложения. По сути это два аргумента и ложатся в две ячейки массива `String[]` но при просмотре массива аргументов понимаем, что следующее значение - номер порта.


----
#### [[Запуск java программы из командной строки - аргрументы и флаги - JVM Diagnostics & Monitoring - Flashcards|Link to flashcards]]



---
### References:

