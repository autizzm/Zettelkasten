
25-09-2025 10:58

Status: #baby 

Tags: [[Java+]] [[Http - Java]]

---
# HttpRequest.Builder - Java

Создаётся с помощью satic метода HttpRequest:
```java
HttpRequest.Builder builder = HttpRequest.newBuilder();
```

Установка типа запроса:

- DELETE()
- GET()
- HEAD()
- ...
- POST(`HttpRequest.BodyHandler badyHandler`)
- PUT(`HttpRequest.BodyHandler badyHandler`)



- timeout(`Duration duration`)
- uri(`URI uri`)
- version(`HttpCliernt.Version version`)


- build() - терминальная операция
- 
----
#### [[HttpRequest.Builder - Java - Flashcards|Link to flashcards]]



---
### References:

- [[HttpRequest - Java]]
