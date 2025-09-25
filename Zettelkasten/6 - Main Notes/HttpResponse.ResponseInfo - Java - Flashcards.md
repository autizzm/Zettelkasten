
Theory for the cards: [[HttpResponse.ResponseInfo - Java]] 

FILE TAGS: java+

Q: Что за класс HttpResponse.ResponseInfo? Что он содержит? Для чего он нужен? И какие методы имеет?
A: HttpResponse.ResponseInfo - wrapper class over the Http response. 
	
Содержит информацию, которая передаётся `HttpResponse.BodyHandler<T>` до того, как будет обработано тело запроса.
	
Методы:
- headers()
- statusCode()
- version() - версия полученного Http ответа
<!--ID: 1758737169512-->

Q: Можно ли из объекта `HttpResponse.ResponseInfo`, который передаётся в метод `apply(HttpResponse.ResponseInfo respInfo)` получить необработанное тело запроса?
A: Нет нельзя, этот интерфейс нужен, чтобы на основе заголовков. статус кода и версии HTTP определить используемый `HttpResponse.BodySubscriber`.
<!--ID: 1758800992293-->

