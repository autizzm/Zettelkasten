
24-09-2025 21:08

Status: #adult

Tags: [[Java+]] 

---
# HttpResponse.BodyHandlers - Java

Все знать не надо.

### Пара примеров:

Преобразуем body в String:

```java
// Receives the response body as a String
HttpResponse<String> response = client
  .send(request, BodyHandlers.ofString());
```

Преобразуем body в file:

```java
// Receives the response body as a file
HttpResponse<Path> response = client
  .send(request, BodyHandlers.ofFile(Paths.get("example.html")));
```

Отбрасываем body (не обрабатываем):
```java
// Discards the response body
HttpResponse<Void> response = client
  .send(request, BodyHandlers.discarding());
```


----
#### [[HttpResponse.BodyHandlers - Java - Flashcards|Link to flashcards]]



---
### References:

- [[HttpResponse.BodyHandler - Java]]