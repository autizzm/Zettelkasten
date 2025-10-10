
09-10-2025 18:15

Status: #baby 

Tags: [[Spring]]

---
# property file - Spring

храним в resources



### Пример property файла

musicPlayer.properties:
```properties
musicPlayer.name=Some name  
musicPlayer.volume=70
```


ApplicationContext.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
       xmlns:context="http://www.springframework.org/schema/context"  
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
       xsi:schemaLocation="  
           http://www.springframework.org/schema/beans           http://www.springframework.org/schema/beans/spring-beans.xsd           http://www.springframework.org/schema/context           http://www.springframework.org/schema/context/spring-context.xsd">  
  
    <context:property-placeholder location="classpath:musicPlayer.properties"/>
    
    beanzzz wtf...
    
</beans>
```

----
#### [[property file - Spring - Flashcards|Link to flashcards]]



---
### References:

- [[Application Context.xml file - Spring]]