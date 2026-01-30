
Theory for the cards: [[Получение параметров запроса - Spring Web MVC]]

FILE TAGS: spring spring_mvc

Q: Что будет если перейти на такой url: `http://localhost:8080/myapp/first/goodbye` (без параметров)? Если метод,обрабатывающий этот запрос выглядит таким образом?
```java
@GetMapping("/hello")
public String helloPage(HttpServletRequest request){
	String name = request.getParameter("name");
	
	...
	
	return "first/hello";
}
```
A: Если не передать параметры в запросе к методу, где стоит HttpServletRequest request -> при вызове `request.getParameter("name");` получим просто null
<!--ID: 1760898502200-->


Q: Что будет если перейти на такой url: `http://localhost:8080/myapp/first/goodbye` (без параметров)? Если метод,обрабатывающий этот запрос выглядит таким образом?
```java
@GetMapping("/hello")
public String helloPage(@RequestParam("name") String name){
		
	...
	
	return "first/hello";
}
```
A: Если не передать параметры в запросе к методу, где стоит @RequestParam("name") -> получим ошибку:
	HTTP Status 400 - Bad Request
	
>  Решение (используем параметр required):
>  ```java
>  @GetMapping("/goodbye")  
>public String goodBye(
>@RequestParam(value = "name", required = false) String name, @RequestParam("surname", required = false) String surname){
>   
>    System.out.println("New user: name=" + name + " surname=" + surname);  
>    return "first/goodbye";  
>}
>  ```
<!--ID: 1760898502210-->

Q: Что тут происходит (с url)? Как он будет выглядеть?
```java
@Controller  
@RequestMapping("/people")  
public class PeopleController {  
	
    @GetMapping("/{id}")  
    public String show(@PathVariable("/id")double id, Model model){  
	  
        return null;  
    }  
}
```
A:  @PathVariable - извлекает параметр из url. Здесь будет такой url: `.../people/67`, где 67 - предаваемый параметр (id)
<!--ID: 1760937373808-->

Q: Какие есть способы передачи параметров в Http запросе и какие есть способы их извлечения в Spring Web MVC?
A:   
1. В теле запроса
	
```java
@ResponseBody
@GetMapping("/sayHello")
public String sayHello() {
	return "Hello, world!";
}
```
	
> [!note]
> Если в методе контроллера, аннотированном `@ResponseBody` возвращать объект, то он будетавтоматически конвертирован в json с помощью Jackson:
> ```java
> @ResponseBody
>@GetMapping("/sayHello")
>public String sayHello() {
>	return Map.of("code", "200", "errorMessage", "");
>}
> ```
	
2. В пути
	
```java
@GetMapping("/{id}")  
public String show(@PathVariable("id")double id, Model model){  
	...
	return null;  
}  
```
	
3. В заголовках
	
```java
@GetMapping("/users") 
public String getUsers( 
	@RequestHeader("Authorization") String authToken, // Обязательный 
	@RequestHeader(value = "User-Agent", required = false) String userAgent, // Необязательный 
	@RequestHeader(value = "X-API-Version", defaultValue = "1.0") String apiVersion
	) { 
	...
 }
```
	
4. В параметрах запроса: `some/path?key1=val1&key2=val2`
	
```java
@PostMapping("/hello")
public String helloPage(@RequestParam("name") String name){
	...
	return "first/hello";
}
```
	
Также извлекать все типы параметров можно через `HttpServletRequest`.
<!--ID: 1769524942013-->
