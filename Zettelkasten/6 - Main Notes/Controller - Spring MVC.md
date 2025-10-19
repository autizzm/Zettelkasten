
19-10-2025 10:50

Status: #baby 

Tags: [[Spring]]

---
# Controller - Spring MVC



При получении запроса [[DispatcherServlet - Spring MVC|DispatcherServlet]] перенаправляет его на соответствующий контроллер.
В контроллере выполняется логика обработки сообщения.


Пример контроллера:
```java
package secret.boy.mvc.app;  
  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.GetMapping;  
  
@Controller  
public class HelloController {  
  
    @GetMapping("/hello-world")  
    public String sayHello(){  
        return "hello_world"; //thymeleaf будет искать представление "hello_world.html"
    }  
}
```


### @Controller

Наследуется от аннотации [[Configuration via Annotations - Spring#@Component|@Component]] => поэтому [[Configuration via Java code - Spring#@ComponentScan("path/to/classes")|@ComponentScan]] работает с ней также как и с @Component:
	Когда при сканировании встречается @Controller - он сохраняется в контекст в качестве bean.

~={cyan}~={purple}@Controller=~ - тот же [[Configuration via Annotations - Spring#@Component|@Component]] , но с дополнительными возможностями.=~


>Обычно (но не всегда), методы возвращают строку String - название того представления, которое надо отправить пользователю.

> [!note] **Rule of a thumb**
> хранить представления в views в папке под каждый контроллер
> ```
> resources
> |--views
> |    |--hello
> |    |    |--world.html
> |    |    |--bitch.html
> |    |--goodbye
> |         |--world.html
> |         |--motherfucker.html
> |--webapp
> |   |--...
> |--...
> ```
> 
> Пример:
> ```java
>@Controller  
>public class HelloController {  
>    @GetMapping("/hello-world")  
>    public String sayHello(){  
>        return "hello/world"; //thymeleaf будет искать представление world.html в папке hello в корневой папке представлений (обычно - "views")
>    }  
>    @GetMapping("/hello-bitch)
>    public String sayHelloBitch(){
>    return "hello/bitch"; //thymeleaf будет искать представление bitch.html в папке hello в корневой папке представлений (обычно - "views")
>    }
>}
> ```


### @GetMapping (Маппинг)


![[Pasted image 20251019193428.png]]


#### Маппинг на уровне контроллера

Для маппинга вего контроллера - используем только @RequestMapping
	И для маппинга на уровне контроллера это релевантный метод
	


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
	~={cyan}GET ..../people/new=~



> @RequestMapping на методе - хуйня, устарело
> 
> @RequestMappong на контроллере - норм тема
>




----
#### [[Controller - Spring MVC - Flashcards|Link to flashcards]]



---
### References:

