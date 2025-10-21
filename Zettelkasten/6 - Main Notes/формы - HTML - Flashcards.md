
Theory for the cards: [[формы - HTML]]

FILE TAGS: html

Q: Что тут не так?
```html
<form method="UPDATE" action="/people">
	<label for="name">Name</label>
	<input name="name" type="text" id="name"/>
	
	<br>
	
	<label for="surname">Surname</label>
	<input name="surname" type="text" id="surname"/>
	
	<br>
	
	<label for="email">Email</label>
	<input name="email" type="text" id="email"/>
	
	<br>
	
	<input type="submit" value="AddPerson"/>
</form>
```
A:  Некорректное значение атрибута method. HTML поддерживает только два метода отправки формы. POST и GET.
<!--ID: 1760978176899-->
