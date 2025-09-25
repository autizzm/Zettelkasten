
23-09-2025 10:40

Status: #child

Tags: [[Java+]] [[Http - Java]]

---
# HttpClient.Builder - Java


Создаётся методом ~={orange}newBuilder()=~ класса [[HttpClient class - Java|HttpClient]]:

```java
HttpClient client = HttpClient.newBuilder()
	.version(HttpClient.Version./*HTTP_1_1 или HTTP_2 */)
	.followRedirects(HttpClient.Redirect./* ALWAYS, NEVER, NORMAL */)
	.connectionTimeout(Duration.ofSeconds(20))
	.proxy(ProxySelector.of(new InetSocketAddr("proxy.example.com", 80)))
	.authenticator(Authenticator.getDefault())
	.build();
```


- ~={green}connectionTimeout(`Duration duration`) =~
	- устанавливает время, через которое, при постоянных неудачных попытках подключения будет выброшено HttpConnectionTimeoutException
	
- ~={green}followRedirects(`HttpClient.Redirect policy`)=~
	- устанавливает политику редиректов:
		- ~={cyan}ALWAYS=~  - всегда позволяем редиректить наш запрос
		- ~={cyan}NEVER=~ - не позволяем
		- ~={cyan}NORMAL=~ - позволяем редиректить всегда, кроме случаев, когда запрос с HTTPS редиректится на HTTP (типа небезопасно)
	
- ~={green}executor(`Executor executor`) =~
	- устанавливает Executor, который будет выполнять async операции
	- by default -> создаётся default Executor




- ~={cyan}Authenticator=~  (java.net.Authenticator) 
	- используется для аутентификации клиента на сервере
	
- ~={cyan}ProxySelector=~ (java.net.ProxySelector)
	- Выбирает прокси для соединения
	




----
#### [[HttpClient.Builder - Java - Flashcards|Link to flashcards]]



---
### References:

- [[HttpClient class - Java]]