
19-10-2025 15:52

Status: #baby

Tags: [[Spring]]

---
# Настройка via Java code - Thymeleaf

```java
  
@Configuration  
@ComponentScan("secret.boy.mvc.app")  
@EnableWebMvc  
public class SpringConfig implements WebMvcConfigurer {  
    //реализуем WevMvcConfigurer если нам нужно по своему сконфигурировать Spring MVC app  
    // в данном случае - мы задаём свой шаблонизатор Thymeleaf вместо стандартного    private final ApplicationContext applicationContext;  
    
    //получаем ApplicationContext как bean
    @Autowired  
    public SpringConfig(ApplicationContext applicationContext){  
        this.applicationContext = applicationContext;  
    }  
	  
	  
	//здесь инжектим этот ApplicationContext в новый bean
    @Bean  
    public SpringResourceTemplateResolver templateResolver(){  
        SpringResourceTemplateResolver springResourceTemplateResolver = new SpringResourceTemplateResolver();  
        springResourceTemplateResolver.setApplicationContext(applicationContext);  
        springResourceTemplateResolver.setPrefix("/WEB-INF/views/");  
        springResourceTemplateResolver.setSuffix(".html");  
        return springResourceTemplateResolver;  
    }  
	  
    @Bean  
    public SpringTemplateEngine templateEngine(){  
        SpringTemplateEngine springTemplateEngine = new SpringTemplateEngine();  
        springTemplateEngine.setTemplateResolver(templateResolver());  
        springTemplateEngine.setEnableSpringELCompiler(true);  
        return springTemplateEngine;  
    }  
	  
	//вот это метод мы переопределяем и добавляем сюда свой шаблонизатор
    @Override  
    public void configureViewResolvers(ViewResolverRegistry registry){  
        ThymeleafViewResolver thymeleafViewResolver = new ThymeleafViewResolver();  
        thymeleafViewResolver.setTemplateEngine(templateEngine());  
        thymeleafViewResolver.setOrder(1);  
        registry.viewResolver(thymeleafViewResolver);  
    }  
  
}
```


----
#### [[Настройка via Java code - Thymeleaf - Flashcards|Link to flashcards]]



---
### References:

