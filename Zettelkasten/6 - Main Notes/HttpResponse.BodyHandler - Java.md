
23-09-2025 16:04

Status: #child 

Tags: [[Java+]]  [[Http - Java]]

---
# `HttpResponse.BodyHandler<T>` - Java
## ~={purple}@FunctionalInterface=~


Передаётся в HttpClient для обработки полученного ответа.

~={green}Ответственен за создание BodySubscriber, который принимает само тело ответа в байтах и преобразует в высокоуровневые типы данных:=~

```java
@FunctionalInterface
interface BodyHandler<T>{
	HttpResponse.BodySubscriber<T> apply(HttpResponse.ResponseInfo respInfo);
}
```

> about the [[HttpResponse.ResponseInfo - Java|HttpResponse.ResponseInfo]]

`HttpResponse.BodyHandler<T>` - ~={green}T=~ - return type:

```java
HttpResponse.BodyHandler<String> // будет превращать полученные данные в строки
```


> [!note]
> Вызывается когда уже известен статус ответа, но еще не извлечено тело
> 



### Т.к. это Functional Interface -> мы можем сами задать тело его метода

Для этого создадим лямбду (можно также явно реализовать этот интерфейс):

```java
BodyHandler<Path> bodyHandler = 
	(HttpResponse.ResponseInfo respInfo) -> respInfo.statusCode() == 200 ? 
		HttpResponse.BodySubscribers.ofFile(Paths.get("/tmp")) :
		HttpResponse.BodySubscribers.replacing(Paths.get("/NULL"));
	
client.sendAsync(request, bodyHandler);
```
Здесь [[Functional Interfaces - Java#Как работает|лямбда является реализацией метода]] apply функционального интерфейса `BodyHandler<Path>`


#### Сама обработка тела сообщения выполняется [[HttpResponse.BodySubscriber - Java|BodySubscriber]]-ом


## В основном BodyHandler-ы берутся готовые - из [[HttpResponse.BodyHandlers - Java|HttpResponse.BodyHandlers]] класса

----
#### [[HttpResponse.BodyHandler - Java - Flashcards|Link to flashcards]]



---
### References:

- [[HttpResponse.ResponseInfo - Java]]
- [[HttpResponse.BodySubscriber - Java]]
- [[HttpResponse.BodyHandler - Java]]