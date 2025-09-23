
Theory for the cards: [[HttpClient.Builder - Java]]

FILE TAGS: java+

Q: Как сделать так, чтобы Http запросы, отдаваемые HttpClient для отправки, отправлялись через определённый прокси?
A: При создании HttpClient установить ProxySelector:
```java
HttpClient client = HttpClient.new Builder()
	.proxy(ProxySelector.of(new InetSocketAddr("proxy.example.com", 80)))
	.build();
```
<!--ID: 1758615118885-->


Q: Какие версии HTTP поддерживает HttpClient?
A: Обе: и HTTP 1.1 и HTTP 2. Версяи Http устанавливается при создании HttpClient в HttpClient.Builder:
```java
HttpClient client = HttpClient.new Builder()
	.version(HttpClient.Version./*HTTP_1 или HTTP_2 */)
	.build();
```
<!--ID: 1758615118907-->


Q: Как установить период времени для попыток новых запросов в HttpClient? (Чтобы, если адрес помер, мы долго не стучались в него)
A: При создании HttpClient указываем connectionTimeout в HttpClient.Builder:
```java
HttpClient client = HttpClient.new Builder()
	.connectionTimeout(Duration.ofSeconds(20))
	.build();
```
<!--ID: 1758615118912-->


Q: Как установить политику редиректов для HttpClient?
A: При создании HttpClient указываем followRedirect в HttpClient.Builder:
```java
HttpClient client = HttpClient.new Builder()
	.followRedirects(HttpClient.Redirect./* ALWAYS, NEVER, NORMAL */)
	.build();
```
- ALWAYS  - всегда позволяем редиректить наш запрос
- NEVER - не позволяем
- NORMAL - позволяем редиректить всегда, кроме случаев, когда запрос с HTTPS редиректится на HTTP (типа небезопасно)
<!--ID: 1758615118917-->
