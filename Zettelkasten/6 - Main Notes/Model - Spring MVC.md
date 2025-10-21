
19-10-2025 22:25

Status: #baby 

Tags: [[Spring]]

---
# Model - Spring MVC



![[Pasted image 20251019222556.png]]

### Model class

Специальный класс Спринга. Инжектится в методы контроллекра автоматически (Не надо указывать [[Configuration via Annotations - Spring#@Autowired|@Autowired]])


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

----
#### [[Model - Spring MVC - Flashcards|Link to flashcards]]



---
### References:

