
19-10-2025 09:00

Status: #baby 

Tags: [[Spring]] [[Tomcat]]

---
# web.xml для Spring MVC


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
		</init-param>    <load-on-startup>1</load-on-startup>  
	</servlet>  
	<servlet-mapping>
		<servlet-name>dispatcher</servlet-name>  
	    <url-pattern>/</url-pattern>  
	</servlet-mapping>  
  
</web-app>
```


----
#### [[web.xml для Spring MVC - Flashcards|Link to flashcards]]



---
### References:

- [[web.xml file - Tomcat]]
