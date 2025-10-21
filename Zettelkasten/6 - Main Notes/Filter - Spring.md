
20-10-2025 21:34

Status: #baby 

Tags: [[Spring]]

---
# Filter - Spring

Фильтр - объект, который перехватывает все входящие запросы.



## Use cases:

Перенаправление запросов на методы с HTTP методами PATCH, PUT, DELETE, т.к. [[формы - HTML#Атрибуты тега `<form>`|из HTML формы мы не можем отправить, что-то кроме POST или GET]]

класс замены web.xml с конфигурацией фильтра для поиска имени метода в спрятанном поле:
```java
public class MySpringMVCDispatcherServletInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {  
    @Override  
    protected Class<?>[] getRootConfigClasses() {  
        return null;  
    }  
  
    @Override  
    protected Class<?>[] getServletConfigClasses() {  
        return new Class[] {SpringConfig.class};  
    }  
  
    @Override  
    protected String[] getServletMappings() {  
        return new String[] {"/"};  
    }  
  
    @Override  
    public void onStartup(ServletContext aServletContext) throws ServletException {  
        super.onStartup(aServletContext);  
  
    }  
  
    private void registerHiddenFieldFilter(ServletContext aContext){  
        aContext.addFilter("hiddenHttpMethodFIlter", new HiddenHttpMethodFilter())  
                .addMappingForUrlPatterns(null, true, "/*");  
    }  
}
```

----
#### [[Filter - Spring - Flashcards|Link to flashcards]]



---
### References:

