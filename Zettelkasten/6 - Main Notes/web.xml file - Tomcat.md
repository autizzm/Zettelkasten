
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

----
#### [[web.xml file - Tomcat - Flashcards|Link to flashcards]]



---
### References:

