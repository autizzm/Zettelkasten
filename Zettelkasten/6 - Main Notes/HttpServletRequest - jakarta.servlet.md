
19-10-2025 20:59

Status: #child

Tags: [[Jakarta.servlet]] [[Java+]]

---
# HttpServletRequest - jakarta.servlet

Создаётся Servlet-контейнером и передаётся в методы [[HttpServlet - jakarta.servlet|HttpServlet]].

#### Получение параметров запроса (GET):
```java
String param = request.getParameter("myParam");
```
Если параметров нет - вернёт null.



#### Получение заголовков:
```java
String contentType = request.getHeader("Content-Type");
```


#### Получение тела запроса (POST):
```java
String specificParam = request.getParameter("specificParameter");
```
Servlet eengine сам спарсит тело запроса и вернёт нужный параметр.

НО, долго, если тело большое.

Альтернативный вариант:
```java
InputStream in = request.getInputStream();
```


----
#### [[HttpServletRequest - jakarta.servlet - Flashcards|Link to flashcards]]



---
### References:

- [[HttpServletResponse - jakarta.servlet]]