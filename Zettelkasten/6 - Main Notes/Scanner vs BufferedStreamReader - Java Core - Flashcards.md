
Theory for the cards: [[Scanner vs BufferedStreamReader - Java Core]]

FILE TAGS: java_core

Q: Идиотская заковырка, но затупить можно знатно. 
В чем тут проблема при чтении строки при помощи `Scanner` после чтения `int`?
```java
Scanner scanner = new Scanner(System.in);
	
System.out.println("Enter your age:");
int age = scanner.nextInt();
System.out.println("Enter your surname:");
String surname = scanner.nextLine();
	
System.out.println("Surname: <" + surname + ">; age: <" + age + ">");
```
A: `Scanner.nextInt()` не потребляет разделитель строки после числа. Поэтому вызов `nextLine()` сразу после `nextInt()` может вернуть пустую строку — это частая причина ошибок при консольном вводе.
	
Соответственно, после ввода возраста (ввели например 20 и Enter нажали) он считает 20 в age и все, что после 20 до нажатия Enter сразу же считает в surname (символ `\n` останется InputStreamReader и этот readLine() считает только его и вернет surname как пустую строку:
```
Enter your age:
21
Enter your surname:
Surname: <>; age: <21>
```
<!--ID: 1791903527052-->
