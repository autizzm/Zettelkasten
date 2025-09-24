
Theory for the cards: 

FILE TAGS: java+

Q: Как происходит получение телав Http ответа? Какой класс за это отвечает?
A: 1. Сначала, при получении HTTP ответа, в HttpClient вызывается `HttpResponse.BodyHandler<T>`
2. В этом `HttpResponse.BodyHandler<T>` вызывается метод `apply(HttpResponse.ResponseInfo respInfo)`, который возвращает `HttpResponse.BodySubscriber<T>`
3. Этот `HttpResponse.BodySubscriber<T>` имеет единственный метод getBody(), при вызове которого мы получаем `CompletionStage<T>` (super interface of `CompletableFuture<T>`)
4. Когда этот `CompletionStage<T>` выполняется (синхронно или асинхронно) -> мы получаем тело запрос. (Выполнение кода приостанавливается до получения результата)
`HttpResponse.BodyHandler<T>` -> `HttpResponse.BodySubscriber<T>` -> `CompletionStage<T>` -> `T`
<!--ID: 1758736947110-->

Q: Как самому установить свою логику обработки тела полученного Http ответа с помощью HttpResponse.BodyHandler?
A:  
```java
BodyHandler<Path> bodyHandler = 
	(HttpResponse.ResponseInfo respInfo) -> respInfo.statusCode() == 200 ? 
		HttpResponse.BodySubscribers.ofFile(Paths.get("/tmp")) :
		HttpResponse.BodySubscribers.replacing(Paths.get("/NULL"));
	
client.sendAsync(request, bodyHandler);
```
Лямбдой реализуем интерфейс BodyHandler, таким образом, устанавливая свою логику в метод apply() интерфейса `BodyHandler<T>`.
	
Напомню, он выглядит так:
```java
@FunctionalInterface
interface BodyHandler<T>{
	HttpResponse.BodySubscriber<T> apply(HttpResponse.ResponseInfo respInfo);
}
```
<!--ID: 1758738097693-->
