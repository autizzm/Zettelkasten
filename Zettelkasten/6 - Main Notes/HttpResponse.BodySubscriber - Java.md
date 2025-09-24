
24-09-2025 20:49

Status: #child 

Tags: [[Java+]]

---
# `HttpResponse.BodySubscriber<T>` - Java

```java
interface BodySubscriber<T>{
	CompletionStage<T> getBody(); // когда возвращаемый CompletionStage<T> выполнится, мы получим тело HTTP сообщения
}
```

~={red}!!!=~ ~={yellow}`CompletionStage<T>` -  super интерфейс `CompletableFuture<T>` =~ ~={red}!!!=~



----
#### [[HttpResponse.BodySubscriber - Java - Flashcards|Link to flashcards]]



---
### References:

- [[HttpResponse.BodyHandler - Java]]
- [[HttpClient class - Java]]