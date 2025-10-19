
19-10-2025 09:07

Status: #baby 

Tags: [[Spring]]

---
# Настройка в applicationContextMVC.xml file - Thymeleaf

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"  
       xmlns:context="http://www.springframework.org/schema/context"  
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
       xsi:schemaLocation="  
       http://www.springframework.org/schema/beans       http://www.springframework.org/schema/beans/spring-beans.xsd       http://www.springframework.org/schema/context       http://www.springframework.org/schema/context/spring-context.xsd        http://www.springframework.org/schema/mvc        http://www.springframework.org/schema/mvc/spring-mvc.xsd">  
  
<context:component-scan base-package="secret.boy.mvc.app"/>  
  
    <mvc:annotation-driven/>  
  
    <bean id="templateResolver" class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">  
        <!-- путь к шаблонам в файловой структуре проекта --> 
        <property name="prefix" value="/WEB-INF/views/"/>  
        <property name="suffix" value=".html"/>  
    </bean>  
    <bean id="templateEngine" class="org.thymeleaf.spring5.SpringTemplateEngine">  
        <property name="templateResolver" ref="templateResolver"/>  
        <property name="enableSpringELCompiler" value="true"/>  
    </bean>  
    <bean class="org.thymeleaf.spring5.view.ThymeleafViewResolver">  
        <property name="templateEngine" ref="templateEngine"/>  
        <property name="order" value="1"/>  
        <property name="viewNames" value="*"/>  
    </bean></beans>
```

Здесь указано, что файлы представлений будут лежать в `"/WEB-INF/views/"` и иметь расширение `.html`.


----
#### [[Настройка в applicationContextMVC.xml file - Thymeleaf - Flashcards|Link to flashcards]]



---
### References:

