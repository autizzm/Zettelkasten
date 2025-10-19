
18-10-2025 20:18

Status: #baby 

Tags: [[Tomcat]] [[Java+]]

---
# web.xml file - Tomcat



```xml
<?xml version="1.0" encoding="UTF-8"?>  
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"  
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee  
                             https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd" version="5.0">  
    <servlet>        
	    <servlet-name>authServlet</servlet-name>  
        <servlet-class>identity.module.AuthServlet</servlet-class>  
    </servlet>   
     
    <servlet-mapping>        
	    <servlet-name>authServlet</servlet-name>  
	    
        <url-pattern>/ping</url-pattern>  
        <url-pattern>/user/login</url-pattern>  
        <url-pattern>/admin/login</url-pattern>  
        <url-pattern>/user/register</url-pattern>  
        <url-pattern>/admin/register</url-pattern>  
        <url-pattern>/refresh</url-pattern>  
    </servlet-mapping>  
    <listener>        
	    <listener-class>
                identity.module.ContextListener  
        </listener-class>  
    </listener>
</web-app>
```


Для вызова authServlet (если имя webapp "identity"):
```bash
http://localhost:8080/identity/ping
## или
http://localhost:8080/identity/user/login
```



## web.xml для Spring

```xml
<!DOCTYPE web-app PUBLIC  
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"  
 "http://java.sun.com/dtd/web-app_2_3.dtd" >  
  
<web-app>  
  
	<servlet>  
	    <servlet-name>dispatcher</servlet-name>  
	    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>  
	    <init-param>      
		    <!-- Указываем путь к xml конфигурации  applicationContextMVC.xml -->  
		    <param-name>contextConfigLocation</param-name>  
		    <param-value>/WEB-INF/applicationContextMVC.xml</param-value>  
	    </init-param>
	    <load-on-startup>1</load-on-startup>  
	</servlet>  
	<servlet-mapping>    <servlet-name>dispatcher</servlet-name>  
    <url-pattern>/</url-pattern>  
	</servlet-mapping>  
	
</web-app>
```

----
#### [[web.xml file - Tomcat - Flashcards|Link to flashcards]]



---
### References:

