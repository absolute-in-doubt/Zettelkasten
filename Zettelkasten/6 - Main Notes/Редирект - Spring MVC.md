
07-01-2026 09:35

Status:

Tags: [[Spring]]

---
# Редирект - Spring MVC

```java
@PostMapping()  
public String create(@ModelAttribute("person") @Valid Person person, BindingResult bindingResult){  
    if(bindingResult.hasErrors())  
        return "people/new";  
  
    System.out.println("successfully saved");  
    peopleRepository.save(person);  
    return "redirect:/people";  
}
```

----
#### [[Редирект - Spring MVC - Flashcards|Link to flashcards]]



---
### References:

