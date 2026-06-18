
19-03-2026 10:14

Status:

Tags: [[Veolcity (Apache Velocity)]] [[Java+]]

---
# How Apache Velocity Works


1. **Initialization:** создание и конфигурация [[Velocity Engine]]
2. **Template loading:** загрузка нужного шаблона (файл или String)
3. **Create Context:** заполнение [[Velocity Context]] нужными данными
4. **Template Merging:** [[Velocity Engine]] достаёт переменные из [[Velocity Context]] и вставляет шаблон
5. **Output Generation:** рендеринг конечного результата. Результат рендеринга может быть записан в файл, отправлен как результат (via Http Response, or other ways) или сразу же отображён.


---
### Пример кода обработки шаблона

```java
import org.apache.velocity.VelocityContext;
import org.apache.velocity.app.VelocityEngine;
import org.apache.velocity.Template;

import java.io.StringWriter;

public class VelocityExample {
	public static main(String[] args){
		//Initialize the VelocityEngine
		VelocityEngine velocityEngine = new VelocityEngine();
		velocityEngine.init();
		
		//Load template
		Template template = velocityEngine.getTemplate("templates/welcome.vm");
		
		//Create the context and add data
		VelocityContext context = new VelocityContext();
		context.put("name", "Alice");
		
		//Merge the template with the context
		StringWriter writer = new StringWriter();
		template.merge(context, writer);
		
		//Output the result
		System.out.println(writer.toString());
	}
}
```


#### Template file:

`welcome.vm`:

```vm
Hello, $name,

Welcome to the Apache Velocity!

Best regards,
Your Team
```


---
### Из строки:

```java
import org.apache.velocity.VelocityContext;
import org.apache.velocity.app.Velocity;
import java.io.StringReader;
import java.io.StringWriter;

VelocityContext context = new VelocityContext();
context.put("name", "World");

String templateString = "Hello $name!";
StringWriter writer = new StringWriter();
Velocity.evaluate(context, writer, "logTag", new StringReader(templateString));
String result = writer.toString();  // "Hello World!"
```



----
#### [[How Apache Velocity Works - Flashcards|Link to flashcards]]



---
### References:

