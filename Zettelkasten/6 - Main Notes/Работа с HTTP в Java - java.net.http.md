
25-09-2025 10:14

Status: #adult 

Tags: [[Java+]]  [[Http - Java]]

---
# Работа с HTTP в Java - java.net.http


1. Создаём [[HttpClient class - Java|HttpClient]] через [[HttpClient.Builder - Java]].
	При создании устанавливаем:
	- версию Http
	- таймаут запроса
	- прокси
	- аутентфикатор (для аутентификации на сервере)
	- политику редиректов
	
2. Создаём [[HttpRequest - Java|HttpRequest]]
	При создании устанавливаем:
	- тип запроса
	- версию Http
	- uri - адрес по которму отправляем запрос
	- headers - добавляем key : value пары заголовков
	
3. Создаём [[HttpRequest.BodyPublisher - Java|HttpRequest.BodyPublisher]] (если отправляем PUT или POST, в них есть body):
	Он преобразует данные, которые мы ему дадим в тело сообщения ~={cyan}(форматирует)=~:
	- Берём готовый в [[HttpRequest.BodyPublishers - Java|HttpRequest.BodyPublishers]]
	
4. Создаём [[HttpResponse.BodyHandler - Java|HttpResponse.BodyHandler<T>]]
	- Либо берём готовый из [[HttpResponse.BodyHandlers - Java|HttpResponse.BodyHandlers]]
	- Либо, если нужна кастомная логика обработки тела -> создаём сами
		- реализуем HttpResponse.BodyHandler лямбдой, которая 
			- принимает [[HttpResponse.ResponseInfo - Java|HttpResponse.ResponseInfo]]
			- возвращает нужный [[HttpResponse.BodySubscriber - Java|HttpResponse.BodySubscriber<T>]] (берём в [[HttpResponse.BodyHandlers - Java|HttpResponse.BodySubscribers]])
	
5. Отправляем запрос через `send(...)` или` sendAsync(...)` ->
получаем [[HttpResponse - Java|HttpResponse<T>]]
6. Закрываем `HttpClient` методом `close()`

## Пример

```java
public class Sender {  
    public static void main(String[] args) {  
        HttpClient client = HttpClient.newBuilder()  
                .version(HttpClient.Version.HTTP_1_1)  
                .followRedirects(HttpClient.Redirect.NORMAL)  
                .build();  
  
        HttpRequest request = HttpRequest.newBuilder()  
                .POST(HttpRequest.BodyPublishers.ofString("{\"msg\":\"hello\"}"))  
                .headers("Content-Type", "application/json")  
                .uri(URI.create("https://httpbin.org/post"))  
                .build();  
  
        HttpResponse.BodyHandler bodyHandler = HttpResponse.BodyHandlers.ofString();  
  
        try {  
            HttpResponse<String> response = client.send(request, bodyHandler);  
            System.out.println(response.body());  
        } catch(InterruptedException | IOException e){  
            e.printStackTrace();  
        }  
        client.close();  
    }  
}
```

----
#### [[Работа с HTTP в Java - java.net.http - Flashcards|Link to flashcards]]



---
### References:

- [[HTTP]]
- [[curl tool]]