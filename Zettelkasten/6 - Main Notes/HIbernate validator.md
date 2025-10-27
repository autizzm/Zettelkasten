
21-10-2025 10:58

Status: #baby 

Tags: [[Hibernate & JPA]] [[Java+]]

---
# HIbernate validator

Это отдельная зависимость ([Hibernate Validator Engine](https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator)). Отдельная часть Hibernate.

> [!note] 
> К каждому полю можно применять сращу несколько аннотаций
> 
### Аннотации

#### @NotEmpty(message="error message")




#### @Size(min=1, max = 30, message="error message)

Устанавливает разрешённый диапазон длины поля. 
Для строковых полей.


### @Min(value=0,, message="error message")

Для числовых полей.


### @Email(message="error message")

Проверяет поле с regex.




## Usage example

```java
public class Person {  
    private int id;  
  
    @NotEmpty(message="name shouldn't be empty")  
    @Size(min=2, max=30, message="Name should be between 2 and 30 characters")  
    private String name;  
  
    @Min(value=0, message="Age can't be negative")  
    private int age;  
  
    @NotEmpty(message="email shouldn't be empty")  
	@Email(message="email must be valid")
    private String email;
    ...
```

**Триггер (активация) проверки при получении объекта из формы:**
(аннотация **@Valid** на получаемом аргументе в handler методе контроллера)
```java
@PostMapping()  
public String create(@ModelAttribute("person") @Valid Person person, BindingResult bindingResult) {
  
    if(bindingResult.hasErrors())  
        return "people/new"; //если есть ошибки - сразу возвращаем пользователя обратно на формуpersonDao.save(person);
          
    personDao.save(person);  
    return "redirect:/people";  
}
```

> [!note]
> BindingResult всегда передаётся после объекта, который валидируем. Туда передаются результаты валидации.

Отображение ошибок в представлении Thymeleaf:
```java
<label for="name">Name: </label>  
<input id="name" type="text" th:field="*{name}"/>  
<div th:if="${#fields.hasErrors('name')}" th:errors="*{name}">Name error</div>
```

----
#### [[HIbernate validator - Flashcards|Link to flashcards]]



---
### References:

