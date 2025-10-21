
20-10-2025 10:00

Status: #baby 

Tags: [[HTML]]

---
# формы - HTML

```html
<form method="POST" action="/my/url">
	<fieldset>
		<input type="text">
	</fieldset>
</form>
```

### Атрибуты тега `<form>`

- **accept-charset**: 
	`<form accept-charsset="ISO-8859-1">`

- **action** - обязательно
	`<form action="mailto: example@gmail.com" enctype="text/plain>`

- **method** - метод отправки (если отправляем по url) (method = "POST/GET/...")
	`<form method="POST" action="/my/url">` 

> [!warning]
> HTML поддерживает только два метода отправки формы. POST и GET.
> 
> Решение: передаём нужный тип запроса в скрытом поле:
> ![[Pasted image 20251020193902.png]]
> Spring resolves special field `"_method"` -> [[Filter - Spring|filter]] directs request to a handler with the specified method 



### `<input>`

#### types:
- text
- email
- url
- number
- date
- ~={green}submit=~ - для отправки формы
- ...

#### атрибуты:

- **accept** - с определённым расширением:
	`<input accept=".git">`

- **autocomplete** = "on/off" - запоминание в автоподстановку

- **disabled** - установить одноразовые взиамодействие - нельзя отменить ввод

- **max/min** - диапазон ввода

- **placeholder** - подсказка

- **pattern** - [[Regular expressions - SQL|regex]] для вводимых знчений

- **required** 

### `<label>`

тег, предназначенный для того, чтобы отметить поле ввода:
~={green}не путать с `input name="surname" placeholder="surname">` - это текст внутри самого поля ввода=~

![[Pasted image 20251020102102.png]]

Тут Name, Surname. Email - указаны в тегах label. код - см. ниже.


### Пример

```html
<form method="POST" action="/people">
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

**Тело отправляемого POST запроса:**
```json
name=ИМЯ&surname=ФАМИЛИЯ&email=EMAIL
```

----
#### [[формы - HTML - Flashcards|Link to flashcards]]



---
### References:

- [[HTML формы в Thymeleaf]]