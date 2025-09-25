
Theory for the cards: [[HttpRequest.Builder - Java]] 

FILE TAGS: java+

Q: Как создать HttpRequest?
A: 1. Создаём HttpRequest.Builder:
```java
HttpRequest.Builder builder = HttpRequest.newBuilder();
//static методом класса HttpRequest
```
2. выполняем насторйки запроса:
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
	
3. build() - терминальная операция:
```java
HttpRequest request = builder.build();
//получаем готовый HttpRequest
```
<!--ID: 1758790298985-->
