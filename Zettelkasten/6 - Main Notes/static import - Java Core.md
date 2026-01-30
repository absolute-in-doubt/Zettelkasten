
16-01-2026 12:31

Status:

Tags:

---
# static import - Java Core

`static import` нужен, чтобы вызывать статические методы и обращаться к статическим полям **без указания имени класса**, что делает код короче и (при разумном использовании) более читаемым.
Основные цели `static import` в Java:

- Упрощение синтаксиса при частом использовании статических членов: можно писать `sqrt(4)` вместо `Math.sqrt(4)`, `assertEquals(...)` вместо `Assert.assertEquals(...)`, `PI` вместо `Math.PI`​
    
- Повышение выразительности кода в доменно-насыщенных местах: тесты (JUnit/TestNG asserts), математические вычисления, утилитные классы, константы (`SECONDS`, `MINUTES`, HTTP‑коды и т.п.).

При этом `static import`:

- Работает только на этапе компиляции и не влияет ни на байткод, ни на производительность; в байткоде всё равно остаются полные квалифицированные имена.[​
    
- При злоупотреблении может ухудшать читаемость (не видно, из какого класса пришёл метод/константа), поэтому рекомендуется использовать его точечно — для часто используемых и хорошо узнаваемых статических членов.


### Пример

```java
import static java.lang.Math.PI;
import static java.lang.Math.sqrt;
import static org.junit.jupiter.api.Assertions.assertEquals;

public class Example {
    public static void main(String[] args) {
        double r = 2;
        double length = 2 * PI * r; // вместо Math.PI
        double root = sqrt(16);     // вместо Math.sqrt(16)
        System.out.println(length + " " + root);
    }
}
```


----
#### [[static import - Java Core - Flashcards|Link to flashcards]]



---
### References:

