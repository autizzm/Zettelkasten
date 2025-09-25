
Theory for the cards: [[Работа с HTTP в Java - java.net.http]] 

FILE TAGS: java+

Q: Опиши процесс отправки Http запроса в Java по шагам.
A:   
1. Создаём `HttpClient` через `HttpClient.Builder`.
	При создании устанавливаем:
	- версию Http
	- таймаут запроса
	- прокси
	- аутентфикатор (для аутентификации на сервере)
	- политику редиректов
	
2. Создаём `HttpRequest`
	При создании устанавливаем:
	- тип запроса
	- версию Http
	- uri - адрес по которму отправляем запрос
	- headers - добавляем key : value пары заголовков
	
3. Создаём `HttpRequest.BodyPublisher` (если отправляем PUT или POST, в них есть body):
	Он преобразует данные, которые мы ему дадим в тело сообщения (форматирует):
	- Берём готовый в `HttpRequest.BodyPublishers`
	
4. Создаём `HttpResponse.BodyHandler`
	- Либо берём готовый из `HttpResponse.BodyHandlers`
	- Либо, если нужна кастомная логика обработки тела -> создаём сами
		- реализуем `HttpResponse.BodyHandler` лямбдой, которая 
			- принимает `HttpResponse.ResponseInfo`
			- возвращает нужный `HttpResponse.BodySubscriber`(берём в `HttpResponse.BodySubscribers`)
	
5. Отправляем запрос через `send(...)` или` sendAsync(...)`  ->
получаем `HttpResponse<T>`
6. Закрываем `HttpClient` методом `close()`
	
	
Пример:
	
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
<!--ID: 1758792397853-->
