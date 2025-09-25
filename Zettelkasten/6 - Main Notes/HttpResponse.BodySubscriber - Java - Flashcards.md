
Theory for the cards: [[HttpResponse.BodySubscriber - Java]]

FILE TAGS: java+

Q: Что делает `HttpResponse.BodySubscriber`?
A: Он превращает тело Http сообщения (сам достаёт его из полученного HTTP ответа) в нужный нам формат и возвращает `CompletionStage<T>` (super interface of `CompletableFuture<T>`), который по завершении обработки вернёт `T`
	
```java
HttpResponse.BodySubscriber bodySubscriber = HttpResponse.BodySubscribers.ofString();
```
<!--ID: 1758800784802-->




