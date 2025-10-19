
Theory for the cards: [[Параметры GET запроса - Spring MVC]]

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
<!--ID: 1760898502210-->
