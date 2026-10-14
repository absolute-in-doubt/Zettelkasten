
13-10-2026 17:45

Status:

Tags: [[Java Core]]

---
# Scanner vs BufferedStreamReader - Java Core


Главное отличие: `Scanner` удобнее для разбора данных, а `BufferedReader` быстрее и проще подходит для чтения текста построчно.


---
## Основные различия

|Критерий|`Scanner`|`BufferedReader` + `InputStreamReader`|
|---|---|---|
|Назначение|Чтение и разбор данных|Чтение текста|
|Чтение строки|`nextLine()`|`readLine()`|
|Чтение чисел|`nextInt()`, `nextDouble()`|Нужно преобразовывать вручную|
|Разделение на токены|Встроенное, по регулярному выражению|Нужно делать самостоятельно|
|Производительность|Обычно ниже из-за разбора и регулярных выражений|Обычно выше|
|Кодировка|Можно указать `Charset`|Можно указать `Charset` в `InputStreamReader`|
|Обработка ошибок|Может выбрасывать `InputMismatchException`|`readLine()` может выбросить `IOException`|


### Пример: чтение числа

С помощью `Scanner`:

```java
Scanner scanner = new Scanner(System.in);

int age = scanner.nextInt();
String name = scanner.next();
```

`Scanner` автоматически распознаёт число и разбивает ввод на токены.

С помощью `BufferedReader`:

```java
BufferedReader reader = new BufferedReader(
    new InputStreamReader(System.in, StandardCharsets.UTF_8)
);

int age = Integer.parseInt(reader.readLine());
String name = reader.readLine();
```

Здесь приходится самостоятельно преобразовывать строку в число. При неверном формате `Integer.parseInt()` выбросит `NumberFormatException`.


## Как это работает внутри

![[Pasted image 20261013174822.png]]

Оба подхода могут читать текст из `System.in`, файлов и других потоков. При этом `Scanner` сам умеет работать с источником и буферизовать чтение, поэтому оборачивать его в `BufferedReader` не нужно.

## Что выбирать на практике?

- `Scanner` — ввод с консоли, небольшие программы, быстрое чтение чисел и слов.
    
- `BufferedReader` — построчное чтение файлов, обработка больших объёмов текста, когда важна производительность.
    
- `BufferedReader` + `Integer.parseInt()` — когда нужен быстрый ввод с контролем над разбором данных.
    

> [!warning] Нюанс для собеседования
>  `Scanner.nextInt()` не потребляет разделитель строки после числа. Поэтому вызов `nextLine()` сразу после `nextInt()` может вернуть пустую строку — это частая причина ошибок при консольном вводе.


----
#### [[Scanner vs BufferedStreamReader - Java Core - Flashcards|Link to flashcards]]



---
### References:

