
Theory for the cards: [[HTTP]]

FILE TAGS: java+

Q: В чем отличие HTTP 1.1 от HTTP 2?
A: HTTP 1.1 - передается в виде текста, а HTTP 2 - в виде бинарных фреймов, при чём эти фреймы передаются не в одном сообщении, а по частям. Разбивка HTTP 2 сообщения идёт таким образом:
- HEADER frame
- DATA frame
- и ещё какое-то количество DATA frame-ов
Каждый фрейм содержит заголовок.
<!--ID: 1758632528403-->


Q: Приведи структура HTTP 1.1 запроса и ответа.
A: **В HTTP/1.1** запрос выглядит как текст:
```java
POST /api/login HTTP/1.1 //start line [Request type] [url] [HTTP version]
Host: example.com //Headers -> key : value
Content-Type: application/json
Content-Length: 34
User-Agent: curl/8.0 // \n
// \n
{"username":"alice","password":"1234"} //body
```
Ответ:
```java
HTTP/1.1 200 OK //start line [HTTP version] [Status Code] [status name]
Date: Mon, 23 Sep 2025 12:00:00 GMT //Headers -> key : value
Content-Type: application/json
Content-Length: 27
Connection: keep-alive// \n
// \n
{"status":"ok","token":"xyz"} //body
```
<!--ID: 1758632528432-->
