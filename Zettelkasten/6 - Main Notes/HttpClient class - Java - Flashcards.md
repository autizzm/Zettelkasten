
Theory for the cards: [[HttpClient class - Java]] 

FILE TAGS: java+

Q: Для чего нужен класс HttpClient? Расскажи всё, что знаешь о нём.
A: HttpClient предназначен для отправки HTTP запросов и получения ответов.
Он реализует AutoCLosable -> может использоваться в try-with-resources.
	
ЖЦ:
	
1. Создаётся с помощью HttpClient.Builder, после этого HttpClient - IMMUTABLE:
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
<!--ID: 1758615118923-->
