
23-09-2025 09:57

Status: #baby - ещё flashcards по отправке запросов бы

Tags: [[Java+]]  [[Http - Java]]

---
# HttpClient class - Java

==thread safe==

Используетс ядля отправки Http запросов и получения ответов.


> [!note] **HttpClient is immutable**
> Создаётся с помощью Builder -> ***после этого неизменяем***

implements [[AutoClosable Interface - Java|AutoClosable]]

-~={cyan} close() =~
	- дожидается завершения отправки уже полученных сообщений и обрабатывает их ответы
	- если HttpClient уже закрыт - ~={orange}НЕ=~ выкидывается Exceptions
	



### Работает как синхронно, так и асинхронно

- ~={cyan}send(`HtppRequest request, HttpResponse.BodyHandler<T> responseHandler`)=~
	- throws InterruptedExceptions

~={cyan}
* sendAsync(`HttpRequest request, HttpResponse.BodyHandler<T> responseHandler`)=~
	* 
	
	
- ~={cyan} sendAsync(`HttpRequest request, HttpResponse.BodyHandler<T> responseHandler, HttpResponse.PushPromiseHandler<T> pushHandler`)=~
	
	
	

### Жизненный цикл

1. Создаётся через [[HttpClient.Builder - Java|HttpClientBuilder]]:

!!! методом 
```java
HttpClient client = HttpClient.newBuilder()
        .version(Version.HTTP_1_1)
        .followRedirects(Redirect.NORMAL)
        .connectTimeout(Duration.ofSeconds(20))
        .proxy(ProxySelector.of(new InetSocketAddress("proxy.example.com", 80)))
        .authenticator(Authenticator.getDefault())
        .build();
```

2. После завершения работы особождаем ресурсы методом `close()`
```java
try{
	client.close();
} catch()
```



----
#### [[HttpClient class - Java - Flashcards|Link to flashcards]]


---
### References:

- [[HttpClient.Builder - Java]]
