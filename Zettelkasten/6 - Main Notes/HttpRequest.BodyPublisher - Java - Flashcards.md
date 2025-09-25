
Theory for the cards: [[HttpRequest.BodyPublisher - Java]] 

FILE TAGS: java+

Q: Расскажи об интерфейсе `HttpRequest.BodyPublisher`.
A: Это интерфейс, реализацию которого можно получить из static factory метода `HttpRequest.BodyPublishers`. Это метод принимает данные и передаёт их в конструктор реализации `HttpRequest.BodyPublisher`:
```java
HttpRequest.BodyPublisher<String> bodyPublisher = HttpRequest.BodyPublishers.ofString("the future body");
```
	
Он обрабатывает полученные данные.
`HttpClient` подписывается на него (методом subscribe) и, `HttpRequest.BodyPublisher` по мере обработки данных загружет их в `ByteBuffer`, откуда `HttpClient` читает их.
<!--ID: 1758801880576-->
