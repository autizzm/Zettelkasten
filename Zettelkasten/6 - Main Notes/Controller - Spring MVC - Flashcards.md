
Theory for the cards: [[Controller - Spring MVC]]

FILE TAGS: spring spring_mvc

Q: Как выглядит запрос для вызова этого метода?
```java
@Controller
@RequestMapping("/people")
public class PersonController{
...
	
@GetMapping("/new")
public String newPerson(Model model){ ... }
}
```
A: Чтобы вызвать этот метод надо сделать запрос: 
	GET ..../people/new
<!--ID: 1760892375584-->


Q: Как сделать общий предварительный путь (prepath) ко всем методам определённого контроллера?
A: Используя аннотацию @RequestMapping:
```java
@Controller
@RequestMapping("/people")
public class PersonController{
...
	
@GetMapping("/new")
public String newPerson(Model model){ ... }
}
```
	
Чтобы вызвать этот метод надо сделать запрос: 
	GET ..../people/new
	
	
> [!note] 
> @RequestMapping на методе - хуйня, устарело
> 
> @RequestMappong на контроллере - норм тема
>
<!--ID: 1760892375593-->


Q: Какое представление и где будет искать Thymeleaf?
```java
@Controller  
public class HelloController {  
   @GetMapping("/hello-world")  
   public String sayHello(){  
       return "hello/world"; //thymeleaf будет искать представление world.html в папке hello в корневой папке представлений (обычно - "views")
   }  
   @GetMapping("/hello-bitch)
   public String sayHelloBitch(){
   return "hello/bitch"; //thymeleaf будет искать представление bitch.html в папке hello в корневой папке представлений (обычно - "views")
   }
}
```
A: Будет искать views/hello/world.html и views/hello/bitch.html.
Пример структуры:
```
resources
|--views
|    |--hello
|    |    |--world.html
|    |    |--bitch.html
|    |--goodbye
|         |--world.html
|         |--motherfucker.html
|--webapp
|   |--...
|--...
```
	
> Желательно хранить предствалени по папкам, соответствующим контроллерам => предстваления, выдаваемые одним контроллером хранятся в месте
<!--ID: 1760894090806-->
