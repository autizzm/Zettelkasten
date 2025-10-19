
19-10-2025 20:47

Status: #baby 

Tags: [[Spring]] 

---
# Параметры GET запроса - Spring MVC


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
@GetMapping("/hello")
public String helloPage(@RequestParam("name") String name){
		
	...
	
	return "first/hello";
}
```

> [!warning]
> Если не передать параметры в запросе к методу, где стоит @RequestParam("name") -> получим ошибку:
>  HTTP Status 400 - Bad Request
>  


----
#### [[Параметры GET запроса - Spring MVC - Flashcards|Link to flashcards]]



---
### References:

