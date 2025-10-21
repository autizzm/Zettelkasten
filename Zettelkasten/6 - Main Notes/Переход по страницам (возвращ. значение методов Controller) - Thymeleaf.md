
20-10-2025 10:57

Status: #baby 

Tags: [[Thymeleaf - шаблонизатор]] [[Spring]]

---
# Переход по страницам (возвращ. значение методов Controller) - Thymeleaf


#### Переход на страницу new.html в rootdirectory (WEB_INF/views/) в папке people 

таким образом, путь: `WEB_INF/views/people/new/html`

```java
@GetMapping("/new")  
public String newPerson(Model model){  
    model.addAttribute("person", new Person());  
    return "people/new";  
}
```

#### Редиректы

```java
   @PostMapping()  
    public String create(@ModelAttribute("person") Person person) {  
        personDao.save(person);  
        return "redirect:/people";  
    }  
}
```

Тут с текущей страницы пользователь будет перенаправлен на url `.../people`.

~={red}!!!=~ ~={yellow}Тут возвращаем url, а не путь к представлению.=~ ~={red}!!!=~



----
#### [[Переход по страницам (возвращ. значение методов Controller) - Thymeleaf - Flashcards|Link to flashcards]]



---
### References:

