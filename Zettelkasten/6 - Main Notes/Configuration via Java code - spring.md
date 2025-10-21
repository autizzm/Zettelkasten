
18-10-2025 17:10

Status: #baby

Tags: [[Spring]]

---
# Configuration via Java code

Заменяет [[Application Context.xml file - Spring|ApplicationContext.xml]] при конфигурации.

Т.е. нам не надо даже двух строчек писать в xml.

Чисто Java code + Annotations.


### Аннотация @Configuration

![[Pasted image 20251018171433.png]]

### @ComponentScan("path/to/classes")

Сканирует все классы и пакеты в "path/to/classes". 


![[Pasted image 20251018171522.png]]



---

Для указания источника конфигурации (теперь это Java class) - используем другой Конфигуратор:
![[Pasted image 20251018171806.png]]

---

### Внедрение значений из файла  .properties

С помощью **@PropertySource("path")**:
![[Pasted image 20251018172716.png]]

Непосредственно внедрение:
- с помощью аннотаций  [[Configuration via Annotations - Spring#@Value - внедрение констант|@Value]]
- через Environment:
```java
@Configuration
@PropertySource("classpath:application.properties")
public class AppConfig {

    @Autowired
    private Environment env;

    @Bean
    public DataSource dataSource() {
        DriverManagerDataSource ds = new DriverManagerDataSource();
        ds.setUrl(env.getProperty("db.url"));
        ds.setUsername(env.getProperty("db.user"));
        ds.setPassword(env.getProperty("db.password"));
        return ds;
    }
}

```


> [!note] **В любом из этих способов необходимо создавать**`PropertySourcesPlaceholderConfigurer`:
> ```java
> @Configuration  
@ComponentScan("secret.boy.crud.app")  
@EnableWebMvc  
@PropertySource("classpath:crud_app.properties")  
public class SpringConfig implements WebMvcConfigurer {  
>  private final ApplicationContext applicationContext;  
  >
>    @Bean  
>    public static PropertySourcesPlaceholderConfigurer propertyConfig() {  
>        return new PropertySourcesPlaceholderConfigurer();  
>    }
>
>	...
> ```
> 

----

## Внедрение зависимостей вручную (без @Autowired)

![[Pasted image 20251018172008.png]]

Как это работает:

- если не указан **@Scope** у бина при создании - он по умолчанию Singleton
	Это значит, что тело **@Bean** вызывается один раз и все последующие вызовы Spring прерывает и возвращает уже имеющийся bean.


----
#### [[Configuration via Java code - spring - Flashcards|Link to flashcards]]



---
### References:

