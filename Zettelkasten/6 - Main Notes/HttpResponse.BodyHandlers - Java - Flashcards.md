
Theory for the cards: [[HttpResponse.BodyHandlers - Java]] 

FILE TAGS: java+

Q: Как с помощью BodyHandler получить тело Http ответа в виде строки?
A:   
```java
HttpResponse.BodyHandler = HttpResponse.BodyHandlers.ofString();
```
<!--ID: 1758737886163-->


Q: Как с помощью BodyHandler получить тело Http ответа в виде файла?
A:   
```java
HttpResponse.BodyHandler = HttpResponse.BodyHandlers.ofFile(Paths.get("example.html"));
```
<!--ID: 1758737886169-->


Q: Как с помощью BodyHandler отбросить тело Http ответа, не обрабатывая его?
A:   
```java
HttpResponse.BodyHandler = HttpResponse.BodyHandlers.discarding();
```
<!--ID: 1758737886174-->
