
19-10-2025 15:06

Status: #baby 

Tags: [[Spring]]

---
# Конфигурация web.xml с помощью Java класса + Spring

### Устаревший вариант

Чтобы заменить web.xml Java классом необходимо:
1. создать класс, который будет реализовывать интерфейс `org.springframework.web.WebApplicationInitializer`
	![[Pasted image 20251019150921.png]]


> [!note]
> Класс, реализующий `org.springframework.web.WebApplicationInitializer` автоматически считывается Spring-ом

### Новый вариант

Используем `AbstractAnnotationConfigDispatcherServletInitializer`


![[Pasted image 20251019151234.png]]


### Пример:

```java
public class MySpringMVCDispatcherServletInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {  
    @Override  
    protected Class<?>[] getRootConfigClasses() {  
        return null;  
    }  
	  
    @Override  
    protected Class<?>[] getServletConfigClasses() {  
        return new Class[] {SpringConfig.class};  //возвращаем класс конфигурации DispatcherServlet - по сути класс конфиигурации ApplicationContext, тот, что с аннотацией @Configuration
    }  
	  
    @Override  
    protected String[] getServletMappings() {  
        return new String[] {"/"};  
    }  
    /** этот метод аналогичен этой части из web.xml
    <servlet-mapping>  
		<servlet-name>dispatcher</servlet-name>  
		<url-pattern>/</url-pattern>  
	</servlet-mapping>
    */
    
}
```


----
#### [[Конфигурация web.xml с помошью Java класса + Spring - Flashcards|Link to flashcards]]



---
### References:

