
Theory for the cards: [[HttpRequest - Java]] 

FILE TAGS: java+

Q: Расскажи о классе  HttpRequest. Как создать его экземпляр? Какие методы у него есть?
A: HttpRequest - класс содержащий информацию о Http запросеЮ который мы хотим отправить.
Создаётся он через `HttpRequest.Builder`:
```java
HttpRequest request = HttpRequest.newBuilder()
	...
	.build()
;
```
Содержит он только getter-методы параметров, котрые мы задали в Builder:
- bodyPublisher()
- headers()
- method()
- timeout()
- uri()
- version()
<!--ID: 1758801354930-->
