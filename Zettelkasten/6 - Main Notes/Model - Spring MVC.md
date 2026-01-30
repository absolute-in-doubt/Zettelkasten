
19-10-2025 22:25

Status: #baby 

Tags: [[Spring]]

---
# Model - Spring MVC



![[Pasted image 20251019222556.png]]


> [!warning]
> Обычный `Model` не переживает редирект: данные из него живут только в текущем запросе и теряются при ответе `redirect:/...`.

### Model class

Специальный класс Спринга. Инжектится в методы контроллекра автоматически 


```java
@GetMapping("/goodbye")  
public String goodBye(  
        @RequestParam(value = "name", required = false) String name,  
        @RequestParam(value="surname", required=false) String surname,  
        Model model){  
	
	//добавление в модель
    model.addAttribute("message", "New user: name=" + name + " surname=" + surname);  
      
    return "first/goodbye";  
}
```

[[Получение данных из Model - Thymeleaf]]


### Обмен даннными при редиректе

##### Одноразовые данные (сообщения об ошибке и т.д.)

```java
@PostMapping("/save")
public String save(@ModelAttribute User user,
                   RedirectAttributes redirectAttributes) {
    // ... сохраняем
    redirectAttributes.addFlashAttribute("message", "Сохранено");
    return "redirect:/users";
}
```


В обработчике `GET /users` атрибут `message` ~={cyan}будет доступен как обычный атрибут модели=~, но только один раз (после первого запроса исчезнет).


#### Постоянные данные (фильтры, состояние и т.п.) обычно:
 
- либо кодируют их в **URL** как query‑параметры (`redirect:/users?page=1&sort=name`),
	
- либо кладут в **сессию** и читают уже в целевом контроллере.


----
#### [[Model - Spring MVC - Flashcards|Link to flashcards]]



---
### References:

