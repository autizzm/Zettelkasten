
Theory for the cards: [[Получение параметров запроса - Spring MVC]]

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

