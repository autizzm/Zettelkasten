
19-10-2025 15:20

Status: #baby 

Tags: [[Spring]]

---
# Configurating Spring MVC via Java code

Всё то же самое, как и при [[Configuration via Java code - Spring|конфигурации обычного Spring app via Java code]]

Но, т.к. нам надо добавить сканирование кода для поиска контроллерови маппингов
(классов с аннотациями @Controller, @GetMapping) -> **используем новую аннотацию @EnableMvc**

```xml
	<!-- заменяет эту строчку из applicationContextMVC.xml -->
	<mvc:annotation-driven/>  
```

Короткий пример:

```java
  
@Configuration  
@ComponentScan("secret.boy.mvc.app")  
@EnableWebMvc  
public class SpringConfig{  
  
  beanzzzzz, wtf....
  
}
```

### [[Настройка via Java code - Thymeleaf|Если надо добавить шаблонизатор, см. тут]]


----
#### [[Configurating Spring MVC via Java code - Flashcards|Link to flashcards]]



---
### References:

- [[Configuration via Java code - Spring]]
