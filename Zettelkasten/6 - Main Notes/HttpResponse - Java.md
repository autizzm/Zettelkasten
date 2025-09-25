
25-09-2025 12:31

Status: #child 

Tags: [[Java+]] [[Http - Java]]

---
# HttpResponse - Java

Возвращается после обработки полученного ответа в методе `send(...) / sendAsync(...)` класса [[HttpClient class - Java|HttpClient]]

Внутри метода `send(...) / sendAsync(...)` [[HttpResponse.BodyHandler - Java|BodyHandler]] создаёт [[HttpResponse.BodySubscriber - Java|BodySubscriber]], который обрабатывает тело запроса -> создаётся `HttpResponse<T>`


## Методы

- `T` body()
- `HttpHeaders` headers()
- `HttpRequest` request() - запрос, на который получен этот ответ
- `int` statusCode()
- `URI` uri()
- `HttpClient.Version` version()


----
#### [[HttpResponse - Java - Flashcards|Link to flashcards]]



---
### References:

- [[Работа с HTTP в Java - java.net.http]]

- [[HttpResponse.BodyHandler - Java]]
- [[HttpResponse.BodySubscriber - Java]]