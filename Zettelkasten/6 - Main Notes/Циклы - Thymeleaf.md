
20-10-2025 08:44

Status: #not_even_born 

Tags: [[Thymeleaf - шаблонизатор]] [[Spring]]

---
# Циклы - Thymeleaf


Цикл + применение метода

```html
<div th:each="person : ${peopleList}">  
    <a th:href="@{/people/{id}(id=${person.getId()})}" th:text="${person.getName()}">user</a>  
    <!--user будет заменено на имя пользователя -->
</div>
```

`th:href="@{/people/{id}(id=${person.getId()})}"` - делаем эту ссылку кликабедьной с динамически изменяемым url: 
	в url `/people/{id}` будет подставлен результат `person.getId()`


----
#### [[Циклы - Thymeleaf - Flashcards|Link to flashcards]]



---
### References:

