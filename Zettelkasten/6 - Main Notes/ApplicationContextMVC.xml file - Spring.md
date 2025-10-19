
19-10-2025 08:58

Status: #baby 

Tags: [[Spring]]

---
# ApplicationContextMVC.xml file - Spring

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
       xmlns:context="http://www.springframework.org/schema/context"  
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="  
       http://www.springframework.org/schema/beans       http://www.springframework.org/schema/beans/spring-beans.xsd       http://www.springframework.org/schema/context       http://www.springframework.org/schema/context/spring-context.xsd        http://www.springframework.org/schema/mvc        http://www.springframework.org/schema/mvc/spring-mvc.xsd">  
  <!-- добавились две схемы для MVC + сверху xlmns:mvc="..." -->
  
  
	<context:component-scan base-package="secret.boy.mvc.app"/>  
	  
	<!-- Включает необходимые аннотации для Spring MVC приложения -->
	<mvc:annotation-driven/>  
  

</beans>
```


----
#### [[ApplicationContextMVC.xml file - Spring - Flashcards|Link to flashcards]]



---
### References:

- [[Application Context.xml file - Spring]]