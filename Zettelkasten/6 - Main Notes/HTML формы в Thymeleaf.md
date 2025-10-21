
20-10-2025 10:30

Status: #baby 

Tags: [[Thymeleaf - шаблонизатор]] [[Spring]]

---
# HTML формы в Thymeleaf

![[Pasted image 20251020103116.png]]

Создаём объект класса Person и передаём в model. А дальше - на странице заполняем.

### Пример формы с Thymeleaf:

```html
<form th:method="POST" th:action="@{/people}" th:object="${person}">  
    <label for="name">Name: </label>  
    <input id="name" type="text" th:field="*{name}"/>  
    <br/>    
    <input type="submit" value="Create!"/>  
</form>
```

- `th:object` - объект в который Thymeleaf **вставит поля автоматически** (это если используем @MethodAttribute, иначе параметры (тут name) будут переданы просто как набор параметров)

- ` th:field="*{name}"` указание, что это поле - атрибут класса и его надо заинжектить

- `th:action` - url по которому будет сделан запрос при отправке формы

> [!note] **В представлении Thymeleaf - МОЖНО использовать `th:aaction="PATCH"`**
> (Любые можно, типо не только POST и GET)
> 
> Thymeleaf автоматически преобразует такую запись в форму со скрытым полем. [[формы - HTML#Атрибуты тега `<form>`|Подробнее тут.]]

---

### @ModelAttribute

#### На уровне метода

```java
@ModelAttribute("headerMessage")
public String populateHeaderMessage() {
	return "Welcome to our website!";
}

//в кадлой моделли этого контроллера будет храниться пара:
"headerMessage": "Welcome to our website!"
```

Добавляет пары ключ-значение во все модели контроллера:

![[Pasted image 20251020103834.png]]

#### На уровне параметра метода

```java
@PostMapping()
public String create(@ModelAttribute("person") Person person) {
	personDao.save(Person);
	return "redirect:/people";
}
```

Заменяет добавление значений вручную (каждое поле принимаем как параметр и сами создаём объект) на получение готового объкта:

![[Pasted image 20251020104334.png]]


Здесь метод create - метод на который редиректим пользователя при нажатии кнопки "submit form"

> [!note] 
> Чтобы заполнять поля объекта в форме с помощью Thymeleaf надо его сначала передать в model:
> ```java
> @GetMapping("/new")  
>public String newPerson(Model model){  
>    model.addAttribute("person", new Person());  
 >   return "people/new";  
>}
> ```

~={pink}**Что если отправить POST запрос без полей Person к методу с параметром `@ModelAttribute("person") Person person`?**=~
	 В модель будет положен новый объект Person со значениями полей по умолчанию
	
	это значит, что добавлять Person в model не обязательно. Можно сделать так:
```java
@GetMapping("/new")  
public String newPerson(@MethodAttribute("person") Person person){    
  return "people/new";  
}
```
- Thymeleaf увидит, что перед входом в метод, он должен передать туда Person, но у него его нет -> создаст новый и передаст.




----
#### [[HTML формы в Thymeleaf - Flashcards|Link to flashcards]]



---
### References:

- [[формы - HTML]]
