
Theory for the cards: [[HttpServletRequest - jakarta.servlet]] 

FILE TAGS: java+

Q: Как создаётся HttpServletRequest? Опиши его ЖЦ.
A: Его создаёт Servlet engine при получении запроса. Он обрабатывает входящий запрос и формирует объект стандартизированного класса - HttpServletRequest.
	
Далее он передаётся в HttpServlet, где user-defined код может извлечь эти данные и обработать. 
<!--ID: 1760897538953-->
