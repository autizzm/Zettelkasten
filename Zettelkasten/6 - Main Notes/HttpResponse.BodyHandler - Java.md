
23-09-2025 16:04

Status: #baby 

Tags: [[Java+]]

---
# `HttpResponse.BodyHandler<T>` - Java
## ~={purple}@FunctionalInterface=~



Передаётся в HttpClient для обработки полученного ответа.

Ответственен за создание BodySubscriber, который принимает само тело ответа в батах и преобразует в высокоуровневые типы данных.


`HttpResponse.BodyHandler<T>` - T - return type:
```java
HttpResponse.BodyHandler<String> // будет превращать полученные данные в строки
```


> [!note]
> Вызывается когда уже известен статус ответ, но еще не извлечено тело
> 



#### Т.к. это Functional Interface -> мы можем сами 

----
#### [[HttpResponse.BodyHandler - Java - Flashcards|Link to flashcards]]



---
### References:

