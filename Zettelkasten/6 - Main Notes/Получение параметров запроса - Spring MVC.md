
19-10-2025 20:47

Status: #baby 

Tags: [[Spring]] 

---
# Получение параметры запроса - Spring MVC


### 1. С помощью объекта [[HttpServletRequest - jakarta.servlet|HttpServletRequest]]

Если нужны не только параметры, а, например сессия, заголовки, cookies и т.д.

```java
@GetMapping("/hello")
public String helloPage(HttpServletRequest request){
	String name = request.getParameter("name");
	
	...
	
	return "first/hello";
}
```

> [!warning]
> Если не передать параметры в запросе к методу, где стоит HttpServletRequest request -> при вызове `request.getParameter("name");` получим просто null
>  


### 2. @RequestParam

Если нужно только параметры запроса.

```java
@PostMapping("/hello")
public String helloPage(@RequestParam("name") String name){
		
	...
	
	return "first/hello";
}
```

> [!warning]
> Если не передать параметры в запросе к методу, где стоит @RequestParam("name") -> получим ошибку:
>  HTTP Status 400 - Bad Request
>  
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
>  


> [!note] 
> Эти способы работают как для **GET**, так и для **POST** - они автоматически парсятся. 
> 
> В [[HttpServletRequest - jakarta.servlet|HttpServletRequest]] можно спарсить их самому.

---

### Указание параметров GET  запроса в ссылке на html странице:

```html
<a href="/myapp/first/hello?name=Tom&surname=Cat">Alternative Hello</a>
```

Обращение к localhost:8080/myapp/hello с параметрами name=Tom и surname=Cat.

> [!note]
> адрес сервера можно не указывать, т.к. сервер и так отправил нам эту страницу


### @PathVariable 

@PathVariable - извлекает параметр из url:

```java
@Controller  
@RequestMapping("/people")  
public class PeopleController {  
	  
    @GetMapping("/{id}")  
    public String show(@PathVariable("id")double id, Model model){  
	  
        return null;  
    }  
}
```

Здесь будет такой url: `.../people/67`, где 67 - предаваемый параметр (id)

----
#### [[Параметры GET запроса - Spring MVC - Flashcards|Link to flashcards]]



---
### References:

